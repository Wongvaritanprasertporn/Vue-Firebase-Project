<template>
  <div id="guessNumber">
    กรุณาเดาตัวเลข 1 - 100<br />
    <form @submit.prevent="toguess">
      <input type="number" v-model="guess" /><br />
      <button type="submit">Guess</button>
      <button type="button" @click="reset">Reset Game</button><br />
    </form>
    {{ message }} <br />
    คุณเดามาแล้ว {{ counter }} ครั้ง <br />
    สถิติการเดาที่ครั้งน้อยที่สุด {{ stat }}
  </div>
</template>

<script>
import firestore from "../main.js";
import firebase from "firebase/app";
import "firebase/auth";

export default {
  name: "guessNumber",
  data() {
    return {
      rand: Math.floor(Math.random() * 100) + 1, // returns a random integer from 1 to 100,
      counter: 0,
      guess: null,
      message: null,
      user: 0,
      authUnsubscribe: 0,
      stat: 0,
      userStat: null,
    };
  },
  created() {
    this.authUnsubscribe = firebase.auth().onAuthStateChanged(
      user => {
        this.user = user;
        console.log(this.user.uid + this.user.email)
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
              console.log(doc.id + " " + doc.data().guessCount);
              this.stat = doc.data().guessCount;
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
    toguess() {
      if (this.message != "คุณเดาถูกต้อง เก่งมาก") {
        this.counter += 1;
        if (this.guess < this.rand) {
          this.message = "มากกว่านั้น";
          this.guess = null;
        } else if (this.guess > this.rand) {
          this.message = "น้อยกว่านั้น";
          this.guess = null;
        } else {
          this.message = "คุณเดาถูกต้อง เก่งมาก";
          const obj = {
            randomNumber : this.rand,
            guessCount : this.counter
          };
          const ref = firestore.collection("users");
          const id = this.user.uid + this.user.email;
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
        }
      },
    reset() {
      this.rand = Math.floor(Math.random() * 100) + 1;
      this.message = null;
      this.guess = null;
      this.counter = 0;
    },
  },
  beforeDestroy() {
    this.authUnsubscribe = null
  },
}
</script>

<style lang="scss" scoped>
#guessNumber {
  border: 2px solid blue;
  border-radius: 7px;
  margin: 10px;
  padding: 10px;
}
</style>
