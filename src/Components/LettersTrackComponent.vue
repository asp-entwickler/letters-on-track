<template>

  <div id="LetterTrackComponentWrapper">

    <label>Track</label>
    <br />

    <div id="LetterTrack" ref="LetterTrackContainer">
    </div>
    <br />

    <div id=leftWrapper>
      <label>Letter</label>
      <br />
      <input type="text" id="LetterStarterPad" title="one letter only" maxlength="1" size="1" />
      <br />
      <input type="button" id="goBtn" v-on:click="goBtnCkicked"  class="btn" value="GO!" />
    </div>

    <div id=rightWrapper>
      <SpeedControlComponent ref="childSpeedComp"></SpeedControlComponent>
      <input type="button" id="stopBtn"  v-on:click="stopBtnCkicked" class="btn" value="STOP" />
    </div>

    <modal v-if="showNoLetterModal" @close="showNoLetterModal = false">
      <p slot="body"> Enter a Letter, please </p>
    </modal>

    <modal v-if="showNoSpaceModal" @close="showNoSpaceModal = false">
      <p slot="body"> Letter will fit on track when will be enough space at the left side of track </p>
    </modal>

    <modal v-if="maxNumLettetsReachedModal" @close="maxNumLettetsReachedModal = false">
      <p slot="body"> Maximum number Letters on track reached </p>
    </modal>

  </div>

</template>

<script>
  import SpeedControlComponent from "./SpeedControlComponent.vue";
  import LetterComponent from "./LetterComponent.vue";
  import { EventBus } from "./../event-bus.js";
  import Vue from "vue";
  import Modal from "./Modal.vue";

  export default {
    name: "LetterTrackComponent",

    data() {
      return {
        lettersCount: 0,
        updateCounter: 0,
        updateInProcess: false,
        componentsSet: [],
        usedLetters: [],
        showNoLetterModal: false,
        showNoSpaceModal: false,
        noSpaceModalHasBeenShownFlag: false,
        maxNumLettetsReachedModal: false
      };
    },
    components: {
      SpeedControlComponent,
      LetterComponent,
      Modal
    },

    mounted: function() {
      this.runLetterMover();
    },

    methods: {
      mountLetterComponent: function(letterValParam, speedValParam) {

        if(this.usedLetters.findIndex(x => x === letterValParam) == -1) {
          this.usedLetters.push(letterValParam);
        } else {
          return;
        }

        var ComponentClass = Vue.extend(LetterComponent);
        var instance = new ComponentClass({
          propsData: { letterTitle: letterValParam }
        });

        instance.$mount();
        this.$refs.LetterTrackContainer.appendChild(instance.$el);
        instance.setLetterSpeed(speedValParam);
        this.componentsSet.push(instance);
      },

      goBtnCkicked: function(event) {
        let cSpeed = this.$refs.childSpeedComp.getSpeedValue();
        let letterValue = document.getElementById("LetterStarterPad").value;

        if (letterValue == "") {
          this.showNoLetterModal = true;
          return;
        }

        let idx = this.componentsSet.findIndex(
          copm => copm.$el.id === letterValue
        );

        if (idx == -1) {

          if (this.componentsSet.length == 0) {
            this.mountLetterComponent(letterValue, cSpeed);
          } else {
            let intervalID = setInterval( () => {
              let isStartSpaceFree = this.checkFreeSpace();
              if (isStartSpaceFree) {
                this.mountLetterComponent(letterValue, cSpeed);
                clearInterval(intervalID);
              }
            }, 300);
          }
        } else {
          this.componentsSet[idx].setLetterSpeed(cSpeed);
        }
      },

      checkFreeSpace: function() {
        let startSpaceFree = false;
        for (let i = 0; i < this.componentsSet.length; i++) {
          if ( parseInt(this.componentsSet[i].getLetterSpeed()) === 0 && !this.noSpaceModalHasBeenShownFlag ) {
            this.showNoSpaceModal = true;
            this.noSpaceModalHasBeenShownFlag = true;
            return false;
          }

          let el = this.componentsSet[i].$el;
          if (el.offsetLeft > el.clientWidth + 3) {
            startSpaceFree = true;
          } else {
            startSpaceFree = false;
          }
        }
        return startSpaceFree;
      },

      runLetterMover: function() {

        this.intervalId = setInterval(() => {
          this.letterMover();
        }, 10);

      },

      stopBtnCkicked: function(event) {
        let letterValue = document.getElementById("LetterStarterPad").value;

        if (letterValue == "") {
          this.showNoLetterModal = true;
          return;
        }

        if(this.usedLetters.findIndex(x => x === letterValue) != -1) {
          this.usedLetters.splice(this.usedLetters.findIndex(x => x === letterValue), 1);
        } 

        let idx = this.componentsSet.findIndex(
          copm => copm.$el.id === letterValue
        );

        if (idx == -1) {
        } else {
          let comp = this.componentsSet[idx];
          this.$refs.LetterTrackContainer.removeChild(comp.$el);
          this.componentsSet.splice(idx, 1);
        }
      },

      //#region - Component movement logic

      letterMover: function () {

        if(this.updateInProcess)
          return;

        for(var i = 0; i < this.componentsSet.length; i++) {
          let cComponent = this.componentsSet[i];
          let speed = cComponent.getLetterSpeed();

          if((11 - parseInt(speed)) <= this.updateCounter ) {
            this.updateInProcess = true;
            cComponent.setLeftOffset();
            this.checkBounces(cComponent);
          }
        }

        if(this.updateCounter == 10)
          this.updateCounter = 0;

        this.updateCounter++;

      },

      leftTrackSideBounced: function(elem) {
        if (elem.$el.offsetLeft <= 0) {
          return true;
        }
      },

      rightTrackSideBounced: function(elem) {
        if (elem.$el.offsetLeft >= 800 - elem.$el.clientWidth) {
          return true;
        }
      },

      checkLeftSideBounced: function(elem) {
        let leftBorder = elem.$el.offsetLeft;
        let componentsSetLength = this.componentsSet.length;
        for (let i = 0; i < componentsSetLength; i++) {
          if (this.componentsSet[i] === elem) continue;

          let counterpartBorder =
            this.componentsSet[i].$el.offsetLeft +
            this.componentsSet[i].$el.clientWidth;

          let imposition = counterpartBorder - leftBorder;
          if (Math.abs(imposition) <= 1) {
            this.componentsSet[i].setGoingRightFlag(
              !this.componentsSet[i].getGoingRightFlag()
            );
            elem.setGoingRightFlag(!elem.getGoingRightFlag());
            elem.setLeftOffset();
          }
        }
      },

      checkRightSideBounced: function(elem) {
        let componentsSetLength = this.componentsSet.length;
        let rightBorder = elem.$el.offsetLeft + elem.$el.clientWidth;
        for (let i = 0; i < componentsSetLength; i++) {
          if (this.componentsSet[i] === elem) continue;

          let counterpartBorder = this.componentsSet[i].$el.offsetLeft;
          let imposition = counterpartBorder - rightBorder;
          if (Math.abs(imposition) <= 1) {
            this.componentsSet[i].setGoingRightFlag(
              !this.componentsSet[i].getGoingRightFlag()
            );
            elem.setGoingRightFlag(!elem.getGoingRightFlag());
            elem.setLeftOffset();
            
          }
        }
      },

      checkBounces: function(comp) {

        if (comp.getGoingRightFlag()) {
          this.checkRightSideBounced(comp);
        } else {
          this.checkLeftSideBounced(comp);
        }

        if (this.rightTrackSideBounced(comp)) {
          comp.setGoingRightFlag(false);
          comp.setLeftOffset();
          this.updateInProcess = false;
          return;
        }

        if (this.leftTrackSideBounced(comp)) {
          comp.setGoingRightFlag(true);
          comp.setLeftOffset();
          this.updateInProcess = false;
          return;
        }

        this.updateInProcess = false;

      }

    }

  }
  

</script>

<style>
  .btn {
    margin: 10px 0px;
    width: 395px;
    height: 50px;
    float: left;
  }

  #LetterTrackComponentWrapper {
    width: 800px;
    padding: 0px;

    position: relative;
  }

  #LetterStarterPad {
    float: left;
    width: 390px;
    margin: 5px 0px 4px 0px;
  }

  #leftWrapper {
    width: 395px;
    border: 0px solid;
    border-color: grey;
    float: left;
    height: 120px;
  }

  #rightWrapper {
    width: 395px;
    border: 0px solid;
    border-color: grey;
    float: right;
    height: 120px;
  }

  #LetterTrack {
    border: 1px solid;
    border-color: grey;
    width: 800px;
    height: 35px;
  }

  label {
    float: left;
    font-size: 14px;
  }
</style>
