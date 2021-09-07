<template v-if="countriesAreLoaded">
  <div class="form">
    <h1>Create account</h1>
    <div class="form-body">
      <div class="container">
        <div class="input">
          <input type="text" placeholder="Login: a-z, 0-9, >=3" v-model="user.login" />
        </div>
        <div class="error-message" v-if="errors.login.length > 0">
          {{ errors.login }}
        </div>
      </div>
      <div class="container">
        <div class="input">
          <input type="text" placeholder="Email" v-model="user.email" />
        </div>
        <div class="error-message" v-if="errors.email.length > 0">
          {{ errors.email }}
        </div>
      </div>
      <div class="container">
        <div class="input">
          <input
            type="password"
            placeholder="Password: >=5, must use A-Z, a-z and 0-9"
            v-model="user.password"
          />
        </div>
        <div class="error-message" v-if="errors.password.length > 0">
          {{ errors.password }}
        </div>
      </div>
      <div class="container">
        <div class="input">
          <input
            type="password"
            placeholder="Confirm your password"
            v-model="user.confirmedPassword"
          />
        </div>
        <div
          class="error-message"
          v-if="errors.passwordConfirmation.length > 0"
        >
          {{ errors.passwordConfirmation }}
        </div>
      </div>
      <div class="container">
        <div class="input">
          <input type="text" placeholder="Real name" v-model="user.realName" />
        </div>
        <div class="error-message" v-if="errors.realName.length > 0">
          {{ errors.realName }}
        </div>
      </div>
      <div class="container">
        <div class="input birth-date">
          <label> Birth date </label>
          <input type="date" class="date" v-model="user.birthDate" />
        </div>
        <div class="error-message" v-if="errors.birthDate.length > 0">
          {{ errors.birthDate }}
        </div>
      </div>
      <div class="container">
        <div class="input country-list">
          <label> Country </label>
          <select v-model="user.country">
            <option disabled value="">Choose country</option>
            <option
              v-for="country in countries"
              :key="country.id"
              v-bind:value="country.id"
            >
              {{ country.name }}
            </option>
          </select>
        </div>
        <div class="error-message" v-if="errors.country.length > 0">
          {{ errors.country }}
        </div>
      </div>
      <div class="container agreement" @click="agreement = !agreement">
        <div class="input agreement">
          <div class="checkbox">
            <i :class="{ 'fas fa-check': agreement }"></i>
          </div>
          <label class="agreement">I agree with terms and conditions</label>
        </div>
        <div class="error-message" v-if="errors.agreement.length > 0">
          Agree to continue
        </div>
      </div>
    </div>
    <button @click="validateAll">
      Sign Up
    </button>
    <div class="link-to-form" @click="switchForm">I already have an account</div>
    <div class="signup-error" v-show="this.errors.signup.length > 0">
      Error! Try again
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {
        login: "",
        email: "",
        password: "",
        confirmedPassword: "",
        realName: "",
        birthDate: "",
        country: "",
      },
      agreement: false,
      valid: true,
      errors: {
        login: "",
        email: "",
        password: "",
        passwordConfirmation: "",
        realName: "",
        birthDate: "",
        country: "",
        agreement: "",
        signup: "",
      },
      showMessages: false,
      countries: [],
      countriesAreLoaded: false,
    };
  },
  methods: {
    validateAll() {
      this.valid = true;

      this.validateLogin();
      this.validateEmail();
      this.validatePassword();
      this.validateRealName();
      this.validateBirthDate();
      this.validateCountry();
      this.checkAgreement();

      if (this.valid) {
        this.sendUserData();
      } else {
        this.user.password = "";
        this.user.confirmedPassword = "";
      }
    },
    validateLogin() {
      this.errors.login = "";

      if (this.user.login === "") {
        this.errors.login = "Requied field";
        this.valid = false;
      } else {
        this.checkUserLoginExistence();
        const re = new RegExp("^[a-z0-9]{3,15}$");

        if (this.user.login.match(re) == null) {
          this.errors.login = "Incorrect format";
          this.valid = false;
        }
      }
    },
    validateEmail() {
      this.errors.email = "";

      if (this.user.email === "") {
        this.errors.email = "Requied field";
        this.valid = false;
      } else {
        this.checkUserEmailExistence();
        const re = new RegExp(
          "[^@ \\t\\r\\n]+@[^@ \\t\\r\\n]+\\.[^@ \\t\\r\\n]+"
        );

        if (this.user.email.match(re) == null) {
          this.errors.email = "Incorrect format";
          this.valid = false;
        }
      }
    },
    validatePassword() {
      this.errors.password = "";
      this.errors.passwordConfirmation = "";

      if (this.user.password === "") {
        this.errors.password = "Requied field";
        this.valid = false;
      } else {
        const re = new RegExp("^(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9]).{5,}$");

        if (this.user.password.match(re) == null) {
          this.errors.password = "Weak password";
          this.valid = false;
        }
      }

      if (this.user.password !== this.user.confirmedPassword) {
        this.errors.passwordConfirmation = "Passwords dont match";
        this.valid = false;
      }
    },
    validateRealName() {
      this.errors.realName = "";

      if (this.user.realName === "") {
        this.errors.realName = "Requied field";
        this.valid = false;
      }
    },
    validateBirthDate() {
      this.errors.birthDate = "";

      if (this.user.birthDate === "") {
        this.errors.birthDate = "Requied field";
        this.valid = false;
      }
    },
    validateCountry() {
      this.errors.country = "";

      if (this.user.country === "") {
        this.errors.country = "Requied field";
        this.valid = false;
      }
    },
    checkAgreement() {
      this.errors.agreement = "";

      if (!this.agreement) {
        this.errors.agreement = "Agree to countinue";
        this.valid = false;
      }
    },
    switchForm() {
      this.$emit("switchForm", "Sign In");
    },
    async fetchCountries() {
      let response = await fetch("/countries");

      if (response.ok) {
        this.countries = await response.json();
      } else {
        alert("HTTP error: " + response.status);
      }
    },
    async checkUserLoginExistence() {

      let response = await fetch(
        `/users/${this.user.login}/check`
      );

      if (response.ok) {
        let data = await response.json();
        if (data.exist) {
          this.errors.login = "Login already exists";
        }
      } else {
        alert("HTTP error" + response.status);
      }
    },
    async checkUserEmailExistence() {

      let response = await fetch(`/users/email/check`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json;charset=utf-8",
        },
        body: JSON.stringify({ email: this.user.email }),
      });

      if (response.ok) {
        let data = await response.json();
        if (data.exist) {
          this.errors.email = "Email already exists";
        }
      } else {
        alert("HTTP error" + response.status);
      }
    },
    async sendUserData() {
      let response = await fetch("/users", {
        method: "POST",
        headers: {
          "Content-Type": "application/json;charset=utf-8",
        },
        body: JSON.stringify({ user: this.user }),
      });

      let result = await response.json();

      if (result.login) {
        this.$emit("login", this.user.login);
      } else {
        this.errors.signup = "Something went wrong. Try again...";
      }
    },
  },
  created() {
    this.fetchCountries();
  },
};
</script>

<style>
@import "./../styles/form.css";

.container {
  display: flex;
  flex-direction: row;

  align-items: center;
  position: relative;
}

.container:hover .agreement:hover {
  cursor: pointer;
}

.error-message {
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 40px;
  width: 300px;
  background-color: rgb(0, 0, 0);
  color: #fff;
  right: -345px;
}

.error-message::before {
  content: "";
  position: absolute;
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 20px 30px 20px 0;
  border-color: transparent rgb(0, 0, 0) transparent transparent;
  left: -30px;
}

.birth-date label,
.country-list label {
  margin-right: 30px;
}

.birth-date input {
  width: 150px;
  text-align: center;
}

.checkbox {
  width: 30px;
  height: 30px;
  border: 2px solid #000;
  border-radius: 5px;
  margin-right: 20px;
}

.link-to-form {
  margin-top: 15px;
  font-weight: 700;
  font-size: 20px;
  text-decoration: underline;
}

.link-to-form:hover {
  cursor: pointer;
}
</style>
