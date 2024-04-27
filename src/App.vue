<template>
  <div id="app">
    <div id="nav">
      <router-link to="/"
        ><img src="../src/assets/logo.png" height="50"
      /></router-link>
      <span v-if="loggedIn">
        <span>{{user.email}}</span>
        <a @click="signOut">Sign out</a>
      </span>
      <span v-else>
        <router-link to="/login">Login</router-link>
        <router-link to="/register">Register</router-link>
      </span>
    </div>
    <router-view />
  </div>
</template>

<script>
import firebase from "firebase/app";
export default {
  data() {
    return {
      loggedIn: false,
      user : 0,
    };
  },
  mounted() {
    firebase.auth().onAuthStateChanged(user => {
      this.loggedIn = !!user;
    });
  },
  created() {
    this.authUnsubscribe = firebase.auth().onAuthStateChanged(
      user => {
        this.user = user;
        alert(this.user.uid + this.user.email)
      },
      err => {
        alert(err);
      }
    )
  },
  methods: {
    async signOut() {
      try {
        const data = await firebase.auth().signOut();
        alert(data);
        this.$router.replace({ name: "login" });
      } catch (err) {
        alert(err);
      }
    }
  }
};
</script>

<style TopHeadertyle lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0px;
}

#nav {
  background-color: lightblue;
  padding: 10px;
  a {
    font-weight: bold;
    color: #2c3e50;
    padding: 0px 10px;
    text-decoration: none;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
  img {
    vertical-align: middle;
  }
  span {
    float: right;
    height: 50px;
  }
}

body {
  margin: 0px;
}

input {
  width: 350px;
  padding: 5px;
  margin: 20px 0px 20px 0px;
  font-size: 15px;
  border: 2px solid blue;
  border-radius: 7px;
}
button {
  padding: 5px;
  margin: 10px 10px 10px 0px;
  font-size: 15px;
  border: 2px solid blue;
  border-radius: 7px;
  background-color: lightblue;
}
.error {
  color: red;
  font-size: 18px;
}
</style>
