<template>
  <div>
    <div
      @touchstart="handleTouchStart"
      @touchend="handleTouchEnd"
      @touchmove="handleTouchMove"
      @mousedown="handleMouseDown"
      :style="`width:${width}px;height:${height}px;border-radius:${width*.8}px;overflow:hidden;border:8px solid #222;box-shadow:4px 5px 10px 0px #151515;background-color:${wheelColor}`"
    >
      <scroll-wheel-canvas
        :width="width"
        :height="height"
        :offset="pixelOffset"
        :ridgeColor="ridgeColor"
        :wheelColor="wheelColor"
        :ridgeSpacing="ridgeSpacing"
      />
    </div>
    <template v-if="debugMode">
      <table>
        <tr>
          <td>Touch Active</td>
          <td>{{active.toString()}}</td>
        </tr>
        <tr>
          <td>Y</td>
          <td>{{lastYPos}}</td>
        </tr>
        <tr>
          <td>Delta</td>
          <td>{{delta}}</td>
        </tr>
        <tr>
          <td>Cumulative Delta</td>
          <td>{{cumulativeDelta}}</td>
        </tr>
        <tr>
          <td>Absolute Position</td>
          <td>{{absolutePosition}}</td>
        </tr>
        <tr>
          <td>Pixel Offset</td>
          <td>{{pixelOffset}}</td>
        </tr>
        <tr>
          <td>Debug</td>
          <td>{{debugMode}}</td>
        </tr>
      </table>
    </template>
  </div>
</template>
<script>
import ScrollWheelCanvas from "./scrollwheel-canvas";
export default {
  name: "scroll-wheel",
  components: { ScrollWheelCanvas },
  data: function() {
    return {
      active: false,
      event: "",
      lastYPos: null,
      delta: 0,
      cumulativeDelta: 0,
      absolutePosition: 0,
      pixelOffset: 0
    };
  },
  props: {
    tickThreshold: {
      type: Number,
      default: 10
    },
    sensitivity: {
      type: Number,
      default: 1
    },
    width: {
      type: Number,
      default: 50
    },
    height: {
      type: Number,
      default: 200
    },
    min: Number,
    max: Number,
    debugMode: {
      type: Boolean,
      default: false
    },
    ridgeSpacing: {
      type: Number,
      default: 10
    },
    ridgeColor: {
      type: String,
      default: "#444"
    },
    wheelColor: {
      type: String,
      default: "#AAA"
    }
  },
  methods: {
    scrollStart(y) {
      this.active = true;
      this.lastYPos = y;
      this.cumulativeDelta = 0;
    },
    scrollMove(y) {
      const pixelDelta = y - this.lastYPos;
      this.delta = pixelDelta * this.sensitivity;
      const absolutePosition = this.absolutePosition - this.delta;
      this.cumulativeDelta += this.delta;
      const direction = this.cumulativeDelta < 0 ? "UP" : "DOWN";
      this.lastYPos = y;
      let vibrationPattern = [];

      // Handle min limit
      if (this.min !== undefined && absolutePosition < this.min) {
        vibrationPattern = 500;
        this.absolutePosition = this.min;
        this.cumulativeDelta = 0;
        this.emitTick({ direction: "MIN" });
      }
      // Handle max limit
      else if (this.max !== undefined && absolutePosition > this.max) {
        vibrationPattern = 500;
        this.absolutePosition = this.max;
        this.cumulativeDelta = 0;
        this.emitTick({ direction: "MAX" });
      }
      // Handle standard move
      else {
        this.pixelOffset += pixelDelta;
        this.absolutePosition = absolutePosition;
        const tickCount = Math.floor(
          Math.abs(this.cumulativeDelta / this.tickThreshold)
        );

        if (tickCount >= 1) {
          for (let i = 0; i < tickCount; i++) {
            this.emitTick({ direction });
            vibrationPattern.push(30);
            vibrationPattern.push(30);
          }
          this.cumulativeDelta = this.cumulativeDelta % this.tickThreshold;
        }
      }
      if (!!navigator && !!navigator.vibrate) {
        navigator.vibrate(vibrationPattern);
      }
    },
    scrollEnd() {
      this.active = false;
      this.cumulativeDelta = 0;
      this.delta = 0;
    },
    handleTouchStart(e) {
      e.preventDefault();
      this.scrollStart(e.touches[0].screenY);
    },
    handleTouchEnd(e) {
      e.preventDefault();
      this.scrollEnd();
    },
    handleTouchMove(e) {
      e.preventDefault();
      this.scrollMove(e.touches[0].screenY);
    },
    handleMouseDown(e) {
      this.scrollStart(e.screenY);
      window.addEventListener("mousemove", this.handleMouseMove);
      window.addEventListener("mouseup", this.handleMouseUp);
    },
    handleMouseMove(e) {
      e.preventDefault();
      if (this.active) {
        this.scrollMove(e.screenY);
      }
    },
    handleMouseUp() {
      this.scrollEnd();
      window.removeEventListener("mousemove", this.handleMouseMove);
      window.removeEventListener("mouseup", this.handleMouseUp);
    },
    emitTick(options) {
      const tickData = {
        absolutePosition: this.absolutePosition,
        delta: this.delta,
        cumulativeDelta: this.cumulativeDelta,
        pixelOffset: this.pixelOffset,
        tickThreshold: this.tickThreshold,
        sensitivity: this.sensitivity,
        ...options
      };
      this.$emit("tick", tickData);
    }
  }
};
</script>
