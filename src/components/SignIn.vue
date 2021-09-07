<template>
  <div class="form">
    <h1>Sign In</h1>
    <div>
      <div class="input">
        <input
          type="text"
          placeholder="Login or email"
          v-model="user.loginOrEmail"
        />
      </div>
      <div class="input">
        <input type="password" placeholder="Password" v-model="user.password" />
      </div>
      <div class="errors"></div>
      <button @click="login">Sign In</button>
      <div class="link-to-form" @click="switchForm">Create new account</div>
      <div>{{ this.error }}</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {
        loginOrEmail: "",
        password: "",
      },
      error: "",
    };
  },
  methods: {
    switchForm() {
      this.$emit("switchForm", "Sign Up");
    },
    async login() {
      let response = await fetch("/login", {
        method: "POST",
        headers: {
          "Content-Type": "application/json;charset=utf-8",
        },
        body: JSON.stringify({ user: this.user }),
      });
      
      if (response.ok) {
        let data = await response.json();
        if (data.logged) {
          this.$emit("login", data.login);
        } else {
          this.error = data.message;
        }
      } else {
        alert("HTTP error" + response.status);
      }
    },
  },
};
</script>
