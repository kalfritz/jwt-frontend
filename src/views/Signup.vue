<template>
  <section class="signupPage">
    <h1 class="text-center">Sign Up</h1>
    <div class="loadingIcon" v-if="showLoading">
      <img src="../assets/loading.svg" alt>
    </div>
    <div
      class="errorMessage"
      v-bind:class="{'alert alert-danger': this.errorMessage}"
      role="alert"
    >{{this.errorMessage}}</div>
    <form v-if="!showLoading" @submit.prevent="signup">
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
        <h6 id="usernameHelp" class="form-text text-muted">
          Username must be longer than 2 characteres and shorter than 30.
          Username can only contain alphanumeric characteres and under_scores.
        </h6>
      </div>
      <div class="form-row">
        <div class="form-group col-md-6">
          <label for="password">Password</label>
          <input
            v-model="user.password"
            type="password"
            class="form-control"
            id="password"
            placeholder="Password"
            aria-describedby="passwordHelp"
            required
          >
          <h6
            id="passwordHelp"
            class="form-text text-muted"
          >Password must be 10 or more characteres long.</h6>
        </div>

        <div class="form-group col-md-6">
          <label for="confirmPassword">Password</label>
          <input
            v-model="user.confirmPassword"
            type="password"
            class="form-control"
            id="confirmPassword"
            placeholder="Confirm Password"
            aria-describedby="confirmPasswordHelp"
            required
          >
          <h6 id="passwordHelp" class="form-text text-muted">Please confirm your password.</h6>
        </div>
      </div>

      <button type="submit" class="btn btn-primary btn-signup-login">Sign Up</button>
    </form>
    <div v-if="!showLoading" class="text-center mt-4 login-signup-haveornot">
      <h5>Already have an account?</h5>
      <router-link :to="{name: 'login'}" role="button">Login</router-link>
    </div>
  </section>
</template>

<script>
import Joi from 'joi';

const SIGNUP_URL = 'https://jwtauthbackend.herokuapp.com/auth/signup';

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
  confirmPassword: Joi.string()
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
      confirmPassword: '',
    },
    showLoading: false,
  }),
  watch: {
    user: {
      handler() {
        this.errorMessage = '';
      },
      deep: true,
    },
  },
  methods: {
    signup() {
      this.errorMessage = '';
      this.showLoading = true;
      if (this.validUser()) {
        const body = {
          username: this.user.username.toString(),
          password: this.user.password.toString(),
        };

        fetch(SIGNUP_URL, {
          method: 'POST',
          body: JSON.stringify(body),
          headers: {
            'content-type': 'application/json',
          },
        })
          .then(response => {
            if (response.ok) {
              return response.json();
            }
            return response.json().then(error => {
              throw new Error(error.message);
            });
          })
          .then(result => {
            localStorage.token = result.token;

            this.showLoading = false;
            this.$router.push('/dashboard');
          })
          .catch(error => {
            this.showLoading = false;
            this.errorMessage = error.message;
          });
      } else {
        this.showLoading = false;
      }
    },
    validUser() {
      if (this.user.password !== this.user.confirmPassword) {
        this.errorMessage = 'Passwords must match.';
        return false;
      }
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
};
</script>

<style>
form {
  width: 50%;
  margin: auto;
  padding-bottom: 1rem;
}
.signupPage {
  min-height: 460px;
}
.btn-signup-login {
  border-radius: 0.1rem;
}
.errorMessage {
  min-height: 50px;
  width: 50%;
  margin: auto;
  text-align: center;
  margin-top: 0.7rem;
  margin-bottom: 0.5rem;
  border-radius: 0.8rem;
}
.loadingIcon {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.login-signup-haveornot {
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
}
</style>
