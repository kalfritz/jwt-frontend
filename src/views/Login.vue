<template>
  <section class="loginPage">
    <h1 class="text-center">Login</h1>
    <div class="loadingIcon" v-if="showLoading">
      <img src="../assets/loading.svg" alt>
    </div>
    <div
      class="errorMessage"
      v-bind:class="{'alert alert-danger d-sm': this.errorMessage}"
      role="alert"
    >{{this.errorMessage}}</div>
    <form v-if="!showLoading" @submit.prevent="login">
      <div class="form-group">
        <label for="username">Username</label>
        <input
          v-model="user.username"
          type="text"
          class="form-control"
          data-js="a"
          id="username"
          aria-describedby="usernameHelp"
          placeholder="Enter your username"
          required
        >
        <h6 id="usernameHelp" class="form-text text-muted">Enter username to login.</h6>
      </div>
      <div class="form-group">
        <label for="password">Password</label>
        <input
          v-model="user.password"
          type="password"
          class="form-control"
          data-js="a"
          id="password"
          aria-describedby="passwordHelp"
          placeholder="Enter your password"
          required
        >
        <h6 id="passwordHelp" class="form-text text-muted">Enter password to login.</h6>
      </div>

      <button type="submit" class="btn btn-primary btn-signup-login">Login</button>
    </form>
    <div v-if="!showLoading" class="text-center mt-4 login-signup-haveornot">
      <h5>Not have an account?</h5>
      <router-link :to="{name: 'signup'}" role="button">Signup</router-link>
    </div>
  </section>
</template>

<script>
import Joi from 'joi';

const LOGIN_URL = 'https://jwtauthbackend.herokuapp.com/auth/login/';

const schema = Joi.object().keys({
  username: Joi.string()
    .regex(/(^[a-zA-Z0-9_]+$)/)
    .min(2)
    .max(30)
    .required(),
  password: Joi.string()
    .min(10)
    .trim()
    .required(),
});
export default {
  data: () => ({
    errorMessage: '',
    user: {
      username: '',
      password: '',
    },
    showLoading: false,
  }),

  methods: {
    login() {
      this.showLoading = true;
      setTimeout(() => {
        if (this.validUser()) {
          const body = {
            username: this.user.username.toString(),
            password: this.user.password.toString(),
          };
          fetch(LOGIN_URL, {
            method: 'POST',
            body: JSON.stringify(body),
            headers: {
              'content-type': 'application/json',
            },
          })
            .then((response) => {
              if (response.ok) {
                return response.json();
              }
              return response.json().then((error) => {
                throw new Error(error.message);
              });
            })
            .then((result) => {
              localStorage.token = result.token;
              setTimeout(() => {
                this.showLoading = false;
                this.$router.push('/dashboard');
              }, 500);
            })
            .catch((error) => {
              setTimeout(() => {
                this.showLoading = false;
                this.errorMessage = error.message;
              }, 500);
            });
        } else {
          this.showLoading = false;
        }
      }, 10);
    },
    validUser() {
      const result = Joi.validate(this.user, schema);
      if (result.error === null) {
        this.errorMessage = '';
        return true;
      }
      if (result.error.message.includes('username')) {
        this.errorMessage = 'Invalid username.';
      } else {
        this.errorMessage = 'Invalid password.';
      }
      return false;
    },
  },
  watch: {
    user: {
      handler() {
        this.errorMessage = '';
      },
      deep: true,
    },
  },
};
</script>

<style>
.loginPage {
  min-height: 460px;
}
.login-signup-haveornot {
  width: 50%;
  margin: auto;
  background-color: grey;
}
</style>
