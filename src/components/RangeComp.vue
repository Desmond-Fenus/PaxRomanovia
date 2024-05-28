<template lang="">
  <div class="inputContainer">
    <input
      type="range"
      class="slider"
      min="1600"
      max="1920"
      step="20"
      value="1600"
      @input="setValue"
      @change="hideValueAfterDelay"
      @mousedown="clearHideTimeout"
      @mouseup="hideValueAfterDelay"
      ref="range"
    />

    <span class="minValInput">1600</span>
    <span :style="{ 'display': displayAttr, 'left': offsetLeft + 'px'}"
      class="curValInput"
      ref="currentValue"
    >{{ yearValue }}</span>
    <span class="maxValInput">1920</span>
  </div>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
    },
  },

  data() {
    return {
      displayAttr: "none",
      offsetLeft: 500,
      yearValue: 1600,
      hideTimeout: null,
      icons: {
        rangemarker: require("@/assets/rangeIcon.png"),
      },
    };
  },
  methods: {
    setValue() {
      const myRange = this.$refs.range;
      this.displayAttr = "";
      
      //Calculating the percentage of the value in between min and max values
      const valueAsPercent =
        (myRange.valueAsNumber - parseInt(myRange.min)) /
        (parseInt(myRange.max) - parseInt(myRange.min));

      // Getting the range slider's bounding rectangle
      const rangeRect = myRange.getBoundingClientRect();

      // Setting the proper "left" attribute for "Value span" of the range
      //this.offsetLeft = rangeRect.left + valueAsPercent * rangeRect.width - (this.$refs.currentValue.offsetWidth / 2) - rangeRect.left;
    
      const thumbPosition = valueAsPercent * rangeRect.width;
      const offsetFromRange = rangeRect.left + thumbPosition;
      const spanWidth = this.$refs.currentValue.offsetWidth;
      
      // Adjusting offsetLeft so that the span is centered under the thumb
      this.offsetLeft = offsetFromRange - (spanWidth);

      // Setting the value of "Value span" of the range
      this.yearValue = myRange.value; 

      // change state vueX
      this.$store.commit("filters/setYear", this.$refs.range.value);
    },
    hideValueAfterDelay() {
      this.clearHideTimeout(); // Clear any existing timeout
      this.hideTimeout = setTimeout(() => {
        this.displayAttr = "none";
      }, 1000);
    },
    clearHideTimeout() {
      if (this.hideTimeout) {
        clearTimeout(this.hideTimeout);
        this.hideTimeout = null;
      }
    },
  },
};
</script>
<style lang="scss" scoped>
input {
  -moz-appearance: none;
  appearance: none;
  background: none;
  cursor: pointer;
  overflow: hidden;
  transition: 0.3s;
}

input[type="range"] {
  -webkit-appearance: none;
  appearance: none;
  background: transparent;
  cursor: pointer;
  width: calc(100% - 60px);
  margin: 0 30px;
  -webkit-appearance: none;
  appearance: none;
}

/***** Chrome, Safari, Opera, and Edge Chromium *****/
input[type="range"]::-webkit-slider-runnable-track {
  background: #90733c;
  height: 0px;
  margin-top: 20px;
  margin-bottom: 20px;
  -webkit-appearance: none;
  appearance: none;
}

/******** Firefox ********/
input[type="range"]::-moz-range-track {
  background: #90733c;
  height: 0px;
  margin-top: 10px;
  -webkit-appearance: none;
  appearance: none;
}
.slider::-webkit-slider-thumb {
  --c: orange; /* active color */
  --g: 0px; /* the gap */
  --l: 0px; /* line thickness*/
  --s: 30px; /* thumb size*/

  height: 30px;
  aspect-ratio: 1;
  border-radius: 50%;
  // box-shadow: 0 0 0 var(--l) inset var(--c);
  border-image: none;
  border-image: linear-gradient(
      #0000 calc(50% - 2px), 
      #90733c 0 calc(50% + 2px),
      #0000 0
    ) // setting the line width
    1/0 100vw/0 calc(100vw + 2px);
  -webkit-appearance: none;
  appearance: none;
  transition: 0.3s;

  box-shadow: 0 0 0 var(--l) inset var(--c);

  margin-top: -14px;
  background-image: url("@/assets/rangeIcon.png");
  background-size: contain;
  background-position: center center;
  background-repeat: no-repeat;
}

.slider::-moz-range-thumb {
  // margin-top: -14px;
  width: 30px;
  height: 30px;
  border: 0;
  margin: 0;
  border-radius: 50%;
  background-image: url("@/assets/rangeIcon.png");
  background-size: contain;
  background-position: center center;
  background-repeat: no-repeat;
  cursor: pointer;
}

.minValInput,
.maxValInput,
.curValInput {
  color: white;
  font-weight: bold;
  display: inline-block;
  margin: 0px 25px;
}
.curValInput{
  position: absolute;
  background: #212121;
  margin-top: -70px;
}
.maxValInput {
  float: right;
}

.inputContainer {
  width: 100%;
}
</style>
