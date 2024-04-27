<template>
  <div>
    <div v-if="loggedIn">
      <guess-number></guess-number>
      <slide-puzzle></slide-puzzle>
    </div>
    <div v-else>
      <h1>กรุณาล็อคอินเพื่อเข้าเล่นเกม</h1>
    </div>
  </div>
</template>

<script>
import firebase from "firebase/app";
import GuessNumber from "../components/guessNumber.vue";
import SlidePuzzle from "../components/slidePuzzle.vue";

export default {
  components: {
    GuessNumber,
    SlidePuzzle
  },
  mounted() {
    firebase.auth().onAuthStateChanged(user => {
      this.loggedIn = !!user;
    });
  },
  data() {
    return {
      loggedIn: false
    };
  }
};
</script>

<style lang="scss" scoped></style>
