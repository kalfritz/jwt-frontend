
<template>
  <div class="home">
    <div class="jumbotron text-center">
      <h1 class="display-3">Authentication!</h1>
      <p class="lead">Learn Auth is fun! (I really think so)</p>
      <hr class="my-4">
      <p>My patience and I working together! &nbsp; LOL &nbsp; &nbsp; ~~Hello from the past.~~</p>
      <p class="lead mt-4">
        <router-link
          v-if="!user"
          class="btn btn-primary btn-lg"
          :to="{name: 'signup'}"
          role="button"
        >Sign Up</router-link>
        <router-link
          v-if="user"
          class="btn btn-primary btn-lg"
          :to="{name: 'dashboard'}"
          role="button"
        >Go to dashboard</router-link>
      </p>
    </div>
  </div>
</template>

<script>
const API_URL = 'https://jwtauthbackend.herokuapp.com/';
export default {
  data: () => ({
    user: null,
  }),
  methods: {
    logout() {
      localStorage.removeItem('token');
      this.$router.push('/login');
    },
  },
  mounted() {
    fetch(API_URL, {
      headers: {
        authorization: `Baerer ${localStorage.token}`,
      },
    })
      .then(res => res.json())
      .then(result => {
        if (result.user) {
          this.user = result.user;
        } else {
          this.logout();
        }
      });
  },
  name: 'home',
};
</script>
