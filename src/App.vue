<template>
  <div id="app">
    <img src="./assets/soley.svg">
    <h4>{{ msg }}</h4>
    <br />
    <LettersTrackComponent></LettersTrackComponent>
  </div>
</template>

<script>

  import LettersTrackComponent from "./Components/LettersTrackComponent.vue";
  import Modal from "./Components/Modal.vue";

  export default {
    name: "app",
    data() {
      return {
        msg: 'Welcome to "Letters on the Track" Vue.js App for Soley.io'
      };
    },
    components: {
      LettersTrackComponent
    }
  };
  
</script>

<style>
  #app {
    font-family: "Avenir", Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>
