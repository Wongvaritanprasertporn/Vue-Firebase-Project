<template>
  <div id="register">
    <form @submit.prevent="pressed">
      <h1>Register</h1>
      <div class="email">
        <input type="email" v-model="email" placeholder="E-mail" />
      </div>
      <div class="password">
        <input type="password" v-model="password" placeholder="Password" />
      </div>
      <button type="submit">Register</button>
      <div v-if="error" class="error">{{ error.message }}</div>
    </form>
  </div>
</template>

<script>
import firebase from 'firebase/app';
import 'firebase/auth'
export default {
  methods: {
    async pressed() {
      try {
        const user = firebase.auth().createUserWithEmailAndPassword(this.email, this.password);
        alert(user)
        this.$router.replace({name:"home"});
      } catch (err) {
          alert(err)
      }
    },
  },
  data() {
    return {
      email: "",
      password: "",
      error: "",
    };
  },
};
</script>

<style lang="scss" scoped>
#register {
  text-align: center;
}
</style>
