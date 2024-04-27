<template>
  <div id="login">
    <h1>Login</h1>
    <form @submit.prevent="pressed">
      <div class="login">
        <input type="email" v-model="email" placeholder="E-mail" />
      </div>
      <div class="password">
        <input type="password" v-model="password" placeholder="Password" />
      </div>
      <button type="submit">Login</button>
    </form>
    <div v-if="error" class="error">{{ error.message }}</div>
    <br />
    <span>
      Need an account? Click here to
      <router-link to="/register">register</router-link>
    </span>
  </div>
</template>

<script>
import firebase from "firebase/app";
import "firebase/auth";
export default {
  data() {
    return {
      email: "",
      password: "",
      error: ""
    };
  },
  methods: {
    async pressed() {
      try {
        const val = await firebase
          .auth()
          .signInWithEmailAndPassword(this.email, this.password);
        alert(val);
        this.$router.replace({ name: "home" });
      } catch (err) {
        alert(err);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
#login {
  text-align: center;
}
</style>
