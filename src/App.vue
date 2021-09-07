<template>
  <div id="app">
    <SignUp
      v-if="!sessionIsActive && currentForm == 'Sign Up'"
      @switchForm="changeForm"
      @login="fetchUserData"
    />
    <SignIn
      v-if="!sessionIsActive && currentForm == 'Sign In'"
      @switchForm="changeForm"
      @login="fetchUserData"
    />
    <UserPage v-if="sessionIsActive" @logout="logout" v-bind:user="user" />
  </div>
</template>

<script>
import SignUp from "./components/SignUp.vue";
import SignIn from "./components/SignIn.vue";
import UserPage from "./components/UserPage.vue";

export default {
  name: "App",
  components: {
    SignUp,
    SignIn,
    UserPage,
  },
  data() {
    return {
      sessionIsActive: false,
      currentForm: "Sign In",
      login: localStorage.getItem("login"),
      user: {},
    };
  },
  methods: {
    changeForm(form) {
      this.currentForm = form;
    },
    async fetchUserData(login) {
      let response = await fetch("/users/" + login);
      if (response.ok) {
        this.user = await response.json();
        localStorage.setItem("login", login);
        this.sessionIsActive = true;
      } else {
        alert("HTTP error: " + response.status);
      }
    },
    logout() {
      localStorage.removeItem("login");
      this.sessionIsActive = false;
      this.currentForm = "Sign In";
    },
  },
  created() {
    if (localStorage.getItem("login")) {
      this.sessionIsActive = true;
      this.fetchUserData(this.login);
    }
  },
};
</script>

<style>
@import "./styles/imports.css";

* {
  margin: 0;
}

body {
  background-color: blueviolet;
  background: -webkit-linear-gradient(
    90deg,
    rgb(178, 154, 207),
    rgb(95, 1, 187)
  );
  background: -moz-linear-gradient(90deg, rgb(178, 154, 207), rgb(95, 1, 187));
  background: linear-gradient(90deg, rgb(178, 154, 207), rgb(95, 1, 187));
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
</style>
