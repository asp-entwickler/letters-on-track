<template>
  <div class="letterBoxStyle" v-bind:id="letterTitle" v-bind:style="{ left: leftOffsetPixels }" >
    {{ letterTitle }}
  </div>
</template>

<script>
  import { EventBus } from "./../event-bus.js";

  export default {
    name: "LetterComponent",
    props: ["letterTitle"],
    data: function() {
      return {
        msg: "",
        letterSpeed: 0,
        leftOffset: 0,
        leftOffsetPixels: "",
        goingRight: true,
      };
    },

    methods: {

      setLeftOffset () {

          if(parseInt(this.letterSpeed) === 0)
            return;

          if (this.goingRight) {
            this.leftOffset++;
          } else {
            this.leftOffset--;
          }

          this.leftOffsetPixels = this.leftOffset + "px";

        },

        getLetterSpeed: function() {
          return this.letterSpeed;
        },

        setLetterSpeed: function(speedParam) {
          this.letterSpeed = speedParam;
          let that = this;
        },

        setGoingRightFlag: function(directionflag) {
          this.goingRight = directionflag;
        },

        getGoingRightFlag: function() {
          return this.goingRight;
        }
    }
};

</script>

<style>

    .letterBoxStyle {

      border: 0px solid;
      border-color: blue;
      width: 20px;
      height: 33px;

      font-size: 18px;
      position: absolute;

      text-align: center;
      vertical-align: middle;
      line-height: 33px;
      overflow: hidden;

    }

</style>
