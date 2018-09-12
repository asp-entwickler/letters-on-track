<template>

  <div id="SpeedControlWrapper">
    <label for="speed" >Speed   {{ speedValue }}</label>
    <br />
    <input type="range" id="speed" name="speed" v-model="speedValue" min="0" max="10" />
  </div>

</template>

<script>

  export default {
    name: 'SpeedControlComponent',
    data: function () {
        return {
          msg: '',
          speedValue: 0
        }
    },
    
    methods: {
      getSpeedValue: function() {
        return this.$data.speedValue;
      }
    }

  }

</script>

<style>

  #speed{

    float: left;
    margin: 5px;
    width: 95%;

  }

</style>
