
<template>
  <div class="slider-pips">
    <input
      v-for="(item, index) in vals"
      :key="index"
      class="slider-pips__range" 
         
      type="range"
      :min="min"
      :max="max"
      :step="step"
      v-model="value[index]"
      @change="updateValue( $event, index )"
      @input="updateValue( $event, index )"
    />
    <ul v-if="floats" class="slider-pips__floats">
      <li
        v-for="(item, index) in vals"
        class="slider-pips__float"  
        :style="{ 'left': floatPos(item) }"
        @click="indexchange(item,index)"
      :key="index">{{item}}</li>
    </ul>
    <div v-if="isRange" class="slider-pips__range-holder">
      <div class="slider-pips__range-selection" :style="{ width: rangeWidth, left: rangeLeft }"></div>
    </div>
    <ul class="slider-pips__pips">
      <li
        class="slider-pips__pip"
        
        v-for="( pip, index ) in pipCount"
        @click="selectPip( index )"
        :style="{ left: pipPos( index ) }"
       :key="index">
        <span
          class="slider-pips__label"
          :class="{ 'slider-pips__label--hidden': !labels }"
        >{{ pipLabel( index ) }}</span>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  name: "calibrationobservation",
  props: {
    values: { type: [Object,Array], default:()=>[] },
    min: { type: Number, default: 0 },
    max: { type: Number, default: 100 },
    step: { type: Number, default: 10 },
    labels: Boolean,
    range: Boolean,
    floats: Boolean
  },
  data() {

    return {
      vals: this.values,
      value:this.values
    };
  },
  watch: {},

  computed: {
    isRange: function isRange() {
      return this.range;
    },
    pipCount: function pipCount() {
      return Math.round((this.fixedMax - this.fixedMin) / this.fixedStep + 1);
    },
    fixedStep: function fixedStep() {
      return this.fixValue(this.step);
    },
    fixedMin: function fixedMin() {
      return this.fixValue(this.min);
    },
    fixedMax: function fixedMax() {
      return this.fixValue(this.max);
    },
    rangeFraction: function rangeFraction() {
      return 100 / (this.fixedMax - this.fixedMin);
    },
    rangeWidth: function rangeWidth() {
      if (this.vals.length === 1) {
        return (this.vals[0] - this.fixedMin) * this.rangeFraction + "%";
      } else {
        return (this.vals[1] - this.vals[0]) * this.rangeFraction + "%";
      }
    },
    rangeLeft: function rangeLeft() {
      if (this.vals.length === 1) {
        return "0%";
      } else {
        return -(this.fixedMin - this.vals[0]) * this.rangeFraction + "%";
      }
    }
  },

  methods: {
    fixValue: function fixValue(value) {
      debugger
      if (this.vals[0] >= this.vals[1]) {
        return;
      }
      if (this.vals[1] < this.vals[0] || this.vals[1] >= this.vals[2]) {
        return;
      }
      if (this.vals[2] < this.vals[1]) {
        return;
      }
      return parseFloat(parseFloat(value).toFixed(1));
    },
    pipPos: function pipPos(index) {
      return index * this.rangeFraction * this.fixedStep + "%";
    },
    floatPos: function floatPos(value) {
      value = this.fixValue(value);
      return -(this.fixedMin - value) * this.rangeFraction + "%";
    },
    pipLabel: function pipLabel(index) {
      return this.fixValue(this.fixedMin + index * this.fixedStep);
    },
    selectPip: function selectPip(index) {
      var pipValue = this.fixValue(this.fixedMin + index * this.fixedStep);
      var handle = this.getClosestHandle(pipValue);
      this.$set(this.vals, handle, pipValue);
    },
    isPipSelected: function isPipSelected(index) {
      var pipValue = this.fixValue(this.fixedMin + index * this.fixedStep);
      return this.vals.includes(pipValue);
    },
    isPipInRange: function isPipInRange(index) {
      var pipValue = this.fixValue(this.fixedMin + index * this.fixedStep);
      return pipValue >= this.fixedMin && pipValue <= this.vals[0];
    },
    updateValue: function updateValue(ev, handle) {
      console.log(handle)
      var pipValue = this.fixValue(ev.target.value);
      if (this.isRange && this.vals.length > 1) {
        if (handle === 0 && pipValue > this.vals[1]) {
          this.$set(this.vals, 0, this.vals[1]);
        } else if (handle === 1 && pipValue < this.vals[0]) {
          this.$set(this.vals, 1, this.vals[0]);
        } else {
          this.$set(this.vals, handle, pipValue);
        }
      } else {
        this.$set(this.vals, handle, pipValue);
      }
    },
    getClosestHandle: function getClosestHandle(value) {
      var closest = this.vals.reduce(function(p, c) {
        return Math.abs(c - value) < Math.abs(p - value) ? c : p;
      });
      return this.vals.indexOf(closest);
    },
    selectedPipClassName: function selectedPipClassName(pipIndex) {
      var className = "";
      var pipValue = this.fixValue(this.fixedMin + pipIndex * this.fixedStep);
      this.vals.forEach(function(v, i) {
        if (v === pipValue) {
          className += " slider-pips__pip--selected--" + (i + 1);
        }
      });
      return className;
    },
    indexchange(item,i){
      console.log(item,i)
    }
  },

  mounted() {}
};
</script>
<style scoped>
.slider-pips {
  position: relative;
  margin: 30px 0;
  min-height: 40px;
  color: #8e9cad;
  font-size: 12px;
}
.slider-pips__range {
  width: 100%;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  height: 4px;
  display: inline-block;
  background: transparent;
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
}
.slider-pips__range::-webkit-slider-runnable-track {
  -webkit-appearance: none;
  appearance: none;
  position: relative;
  background: #8e9cad;
  height: 4px;
  border-radius: 4px;
}
.slider-pips__range::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  position: relative;
  width: 12px;
  height: 12px;
  background: #6ef4a4;
  box-shadow: 0 0 0 2px #434d5a;
  border-radius: 100%;
  -webkit-transform: translateY(-4px);
  transform: translateY(-4px);
  z-index: 1;
}
.slider-pips__range:focus,
.slider-pips__range:focus::-webkit-slider-runnable-track {
  outline: none;
}
.slider-pips__range-selection {
  position: absolute;
  top: 0px;
  height: 4px;
  background: #6ef4a4;
  border-radius: 3px;
  transition: all 0.2s ease-out;
}
.slider-pips__pips,
.slider-pips__floats {
  position: relative;
  list-style: none;
  margin: 0 6px;
}
.slider-pips__pips {
  top: 12px;
}
.slider-pips__pip {
  position: absolute;
  top: -5px;
  left: 0;
  width: 1px;
  height: 6px;
  background: currentColor;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  transition: all 0.5s ease;
  cursor: pointer;
}
.slider-pips__pip--selected {
  color: #86f6b3;
  height: 10px;
  transition: all 0.05s ease;
}
.slider-pips__pip--selected--2 {
  color: #e69ffa;
}
.slider-pips__pip--selected--3 {
  color: #f59d71;
}
.slider-pips__pip--selected--4 {
  color: #33d5ff;
}
.slider-pips__label {
  position: absolute;
  left: 50%;
  top: 100%;
  -webkit-transform: translate(-50%, 0px);
  transform: translate(-50%, 0px);
  padding: 3px;
}
.slider-pips__label--hidden {
  display: none;
}
.slider-pips__float {
  position: absolute;
  top: -20px;
  -webkit-transform: translateX(-15px);
  transform: translateX(-15px);
  width: 30px;
  text-align: center;
  pointer-events: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  z-index: 1;
}
.slider-pips__float--2 {
  z-index: 2;
}
.slider-pips__float--3 {
  z-index: 2;
}
.slider-pips__float--4 {
  z-index: 2;
}

.slider-pips__range--2::-webkit-slider-thumb {
  z-index: 2;
}
.slider-pips__range--2.slider-pips__range::-webkit-slider-thumb {
  background: #d96ff7;
}

.slider-pips__range--3::-webkit-slider-thumb {
  z-index: 2;
}
.slider-pips__range--3.slider-pips__range::-webkit-slider-thumb {
  background: #f27c41;
}

.slider-pips__range--4::-webkit-slider-thumb {
  z-index: 2;
}
.slider-pips__range--4.slider-pips__range::-webkit-slider-thumb {
  background: #00cbff;
}

.huge.slider-pips {
  margin-top: 30px;
}
.huge.slider-pips ::-webkit-slider-thumb {
  height: 15px;
  margin-top: -2px;
  width: 26px;
  border-radius: 3px;
}
.huge.slider-pips .slider-pips__pip {
  height: 5px;
  width: 2px;
  transition-duration: 1s;
}
.huge.slider-pips .slider-pips__pip:nth-child(5n + 1) {
  height: 9px;
}
.huge.slider-pips .slider-pips__pip--selected {
  height: 12px !important;
  transition: none;
}
.huge.slider-pips .slider-pips__float {
  top: -4px;
  color: black;
}
.huge.slider-pips .slider-pips__pips,
.huge.slider-pips .slider-pips__floats {
  margin: 0 13px;
}

.slider-pips.gradient .slider-pips__range-selection {
  background: linear-gradient(90deg, #6ef4a4 40%, #d96ff7 60%);
}
</style>
