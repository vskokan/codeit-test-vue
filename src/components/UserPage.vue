<template>
  <div class="profile">
    <div class="header">About {{ user.login }}</div>
    <div class="container">
      <div class="field">Email:</div>
      <div class="value">{{ user.email }}</div>
    </div>
    <div class="container">
      <div class="field">Real name:</div>
      <div class="value">{{ user.realName }}</div>
    </div>
    <div class="container">
      <div class="field">Birth Date:</div>
      <div class="value">{{ user.birthDate }}</div>
    </div>
    <div class="container">
      <div class="field">Registration Date:</div>
      <div class="value">{{ dateFormat(user.registrationDate) }}</div>
    </div>
    <div class="container">
      <div class="field">Country:</div>
      <div class="value">{{ user.country }}</div>
    </div>
    <button @click="logout">Sign Out</button>
  </div>
</template>

<script>
export default {
  props: ["user"],
  watch: {
    user: function (newVal) {
      return newVal;
    },
  },
  methods: {
    logout() {
      this.$emit("logout", this.user);
    },
    dateFormat(timestamp) {
      let date = new Date(timestamp);
      const months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "November",
        "October",
        "November",
        "December",
      ];
      return `${date.getDate()} ${
        months[date.getMonth()]
      } ${date.getFullYear()}`;
    },
  },
};
</script>

<style>
.profile {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #fff;
  padding: 40px;
  border-radius: 10px;
  font-family: "Ubuntu", sans-serif;
  font-size: 26px;
  font-weight: 500;
  border: 3px solid rgb(55, 0, 80);
}

.header {
  font-weight: 700;
  font-size: 40px;
  position: relative;
  bottom: 10px;
}

.container {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
}
.field {
  font-weight: 600;
}
.value {
  margin-left: 20px;
}

button {
  margin-top: 50px;
}
</style>