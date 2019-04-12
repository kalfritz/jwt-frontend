<template>
  <div id="app">
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
      <router-link
        class="btn btn-primary btn-lg home-link home"
        :to="{name: 'home'}"
        role="button"
      >Home</router-link>
      <router-link
        class="btn btn-primary btn-lg signup-link"
        :to="{name: 'signup'}"
        role="button"
      >Sign Up</router-link>
      <router-link
        class="btn btn-primary btn-lg login-link"
        :to="{name: 'login'}"
        role="button"
      >Login</router-link>
    </nav>
    <router-view class="container pt-2"/>
  </div>
</template>

<style>
body {
  width: 100%;
}
.navbar {
  position: relative;
}
.signup-link {
  position: absolute;
  left: 80%;
}
.login-link {
  position: absolute;
  left: 88%;
}
</style>
