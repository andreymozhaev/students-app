<template>
  <div id="app">
    <nav>
      <router-link to="/"
        ><img src="./assets/chistov.jpg" alt="logo"
      /></router-link>
      <h2>Чистов Андрей Александрович</h2>
      <router-link to="/add"
        ><img src="./assets/signup.png" alt="signup"
      /></router-link>
    </nav>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {};
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
nav {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  height: 20vh;
  padding: 4vh 0;
}

.link {
  position: absolute;
  bottom: 0;
  right: 0;
}

.link img {
  width: 100px;
}

nav a {
  height: 100%;
}

nav img {
  height: 100%;
  border-radius: 50%;
}
</style>
