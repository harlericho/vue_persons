<template>
  <h2 class="p-3">Person Vue</h2>
  <img id="img" alt="Vue logo" src="./assets/logo.png"/>
  <Person msg="Welcome to Your Vue.js App" />
</template>

<script>
import Person from "./components/Person.vue";
export default {
  name: "App",
  components: {
    Person,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 10px;
}
#body {
  background-color: #282c34;
}
#img {
  width: 100px;
}
h2 {
  color: #fff;
}
#margin {
  margin-right: 3%;
}
#card {
  background: #282c34;
  border-color: #fff;
}
#card-body {
  color: #fff;
}
</style>
