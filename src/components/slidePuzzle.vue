<template>
  <div id="slide-puzzle">
    <div id="table" style="display: table;">
      <div id="row1" style="display: table-row;">
        <div id="cell11" class="tile1" @click="clickTile(1, 1)"></div>
        <div id="cell12" class="tile2" @click="clickTile(1, 2)"></div>
        <div id="cell13" class="tile3" @click="clickTile(1, 3)"></div>
        <div id="cell14" class="tile4" @click="clickTile(1, 4)"></div>
      </div>
      <div id="row2" style="display: table-row;">
        <div id="cell21" class="tile5" @click="clickTile(2, 1)"></div>
        <div id="cell22" class="tile6" @click="clickTile(2, 2)"></div>
        <div id="cell23" class="tile7" @click="clickTile(2, 3)"></div>
        <div id="cell24" class="tile8" @click="clickTile(2, 4)"></div>
      </div>
      <div id="row3" style="display: table-row;">
        <div id="cell31" class="tile9" @click="clickTile(3, 1)"></div>
        <div id="cell32" class="tile10" @click="clickTile(3, 2)"></div>
        <div id="cell33" class="tile11" @click="clickTile(3, 3)"></div>
        <div id="cell34" class="tile12" @click="clickTile(3, 4)"></div>
      </div>
      <div id="row4" style="display: table-row;">
        <div id="cell41" class="tile13" @click="clickTile(4, 1)"></div>
        <div id="cell42" class="tile14" @click="clickTile(4, 2)"></div>
        <div id="cell43" class="tile15" @click="clickTile(4, 3)"></div>
        <div id="cell44" class="tile16" @click="clickTile(4, 4)"></div>
      </div>
    </div>
    <button @click="shuffle">New Game</button>
    <stopwatch ref="stopwatch" @onClock="onClock"></stopwatch>
    เวลาต่ำสุดที่สามารถแก้ปริศนาได้ {{ stat }}
  </div>
</template>

<script>
import firestore from "../main.js";
import firebase from "firebase/app";
import "firebase/auth";
import stopwatch from "./stopwatch.vue";

export default {
  components: {
    stopwatch,
  },
  name: "slidePuzzle",
  data() {
    return {
      isCompleted: false,
      user: 0,
      stat: 0,
      solvedTime: 0,
    };
  },
  created() {
    this.authUnsubscribe = firebase.auth().onAuthStateChanged(
      user => {
        this.user = user;
        console.log(this.user.uid + this.user.email);
      },
      err => {
        console.log(err);
      }
    );
    setTimeout(() => {
      const ref = firestore.collection("users");
      ref.onSnapshot(
        (snapshot) => {
          snapshot.forEach((doc) => {
            if (doc.id == this.user.uid + this.user.email) {
              console.log(doc.id + " " + doc.data().lowestTime);
              this.stat = doc.data().lowestTime;
              console.log(this.stat);
            }
          });
        },
        (err) => {
          console.log(err);
        }
      );
    }, 3000);
  },
  methods: {
    onClock(value) {
      this.solvedTime = value;
    },
    swapTiles(cell1, cell2) {
      var temp = document.getElementById(cell1).className;
      document.getElementById(cell1).className = document.getElementById(
        cell2
      ).className;
      document.getElementById(cell2).className = temp;
      this.checkGame();
    },

    shuffle() {
      this.$refs.stopwatch.reset();
      this.$refs.stopwatch.start();
      //Use nested loops to access each cell of the 4x4 grid
      for (var row = 1; row <= 4; row++) {
        //For each row of the 4x4 grid
        for (var column = 1; column <= 4; column++) {
          //For each column in this row

          var rand_row = Math.floor(Math.random() * 4 + 1); //Pick a random row from 1 to 4
          var rand_column = Math.floor(Math.random() * 4 + 1); //Pick a random column from 1 to 4

          this.swapTiles("cell" + row + column, "cell" + rand_row + rand_column); //Swap the look & feel of both cells
        }
      }
    },

    checkGame() {
      let isTilesMatching = true;

      for (let i = 1; i <= 4; i++) {
        for (let j = 1; j <= 4; j++) {
          const cellClassName = document.getElementById(`cell${i}${j}`).className;
          const expectedTileClassName = `tile${(i - 1) * 4 + j}`;

          if (cellClassName !== expectedTileClassName) {
            isTilesMatching = false;
            break;
          }
        }
      }

      if (isTilesMatching) {
        this.$refs.stopwatch.stop();
        this.isCompleted = true;
        const obj = {
          solvedTime: this.solvedTime,
        };
        const ref = firestore.collection("users");
        const id = this.user.uid + this.user.email;
        console.log(obj);
        if (this.stat == undefined) {
          ref
            .doc(id)
            .add(obj)
            .then(() => {
              console.log("add successfully.");
            })
            .catch((err) => console.log(err));
        }
        else if (this.counter < this.stat) {
          ref
            .doc(id)
            .update(obj)
            .then(() => {
              console.log("update successfully.");
            })
            .catch((err) => console.log(err));
        }
      }
    },
    clickTile(row, column) {
      if (this.isCompleted == false) {
        var cell = document.getElementById("cell" + row + column);
        var tile = cell.className;
        if (tile != "tile16") {
          //Checking if white tile on the right
          if (column < 4) {
            if (
              document.getElementById("cell" + row + (column + 1)).className ==
              "tile16"
            ) {
              this.swapTiles(
                "cell" + row + column,
                "cell" + row + (column + 1)
              );
              return;
            }
          }
          //Checking if white tile on the left
          if (column > 1) {
            if (
              document.getElementById("cell" + row + (column - 1)).className ==
              "tile16"
            ) {
              this.swapTiles(
                "cell" + row + column,
                "cell" + row + (column - 1)
              );
              return;
            }
          }
          //Checking if white tile is above
          if (row > 1) {
            if (
              document.getElementById("cell" + (row - 1) + column).className ==
              "tile16"
            ) {
              this.swapTiles(
                "cell" + row + column,
                "cell" + (row - 1) + column
              );
              return;
            }
          }
          //Checking if white tile is below
          if (row < 4) {
            if (
              document.getElementById("cell" + (row + 1) + column).className ==
              "tile16"
            ) {
              this.swapTiles(
                "cell" + row + column,
                "cell" + (row + 1) + column
              );
              return;
            }
          }
        }
      }
    },
  },
};
</script>

<style lang="scss" scoped>
#slide-puzzle {
  border: 2px solid blue;
  border-radius: 7px;
  margin: 10px;
  padding: 10px;
}

#table {
  margin-left: auto;
  margin-right: auto;
}

.tile1,
.tile2,
.tile3,
.tile4,
.tile5,
.tile6,
.tile7,
.tile8,
.tile9,
.tile10,
.tile11,
.tile12,
.tile13,
.tile14,
.tile15,
.tile16 {
  display: table-cell;
  width: 90px;
  height: 90px;
  border: 1px solid white;
  background: url("https://i.imgur.com/CmFuOpx.png");
  cursor: pointer;
}

.tile1 {
  background-position: 0px 0px;
}

.tile2 {
  background-position: -90px 0px;
}

.tile3 {
  background-position: -180px 0px;
}

.tile4 {
  background-position: -270px 0px;
}

.tile5 {
  background-position: 0px -90px;
}

.tile6 {
  background-position: -90px -90px;
}

.tile7 {
  background-position: -180px -90px;
}

.tile8 {
  background-position: -270px -90px;
}

.tile9 {
  background-position: 0px -180px;
}

.tile10 {
  background-position: -90px -180px;
}

.tile11 {
  background-position: -180px -180px;
}

.tile12 {
  background-position: -270px -180px;
}

.tile13 {
  background-position: -0px -270px;
}

.tile14 {
  background-position: -90px -270px;
}

.tile15 {
  background-position: -180px -270px;
}

.tile16 {
  background: white;
  cursor: default;
}
</style>
