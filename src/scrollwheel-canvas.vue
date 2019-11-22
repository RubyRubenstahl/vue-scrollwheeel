<template>
  <canvas ref="swcanvas" :width="width" :height="height"></canvas>
</template>
<script>
export default {
  name: "scroll-wheel-canvas",
  props: {
    width: {
      type: Number,
      required: true
    },
    height: {
      type: Number,
      required: true
    },
    offset: {
      type: Number,
      default: 0
    },
    ridgeSpacing: {
      type: Number,
      default: 10
    },
    ridgeColor: {
      type: String,
      default: "#999"
    },
    wheelColor: {
      type: String,
      default: "#EEE"
    }
  },
  mounted() {
    this.updateCanvas();
  },
  watch: {
    offset() {
      this.updateCanvas();
    },
    width() {
      this.updateCanvas();
    },
    height() {
      this.updateCanvas();
    }
  },
  methods: {
    updateCanvas() {
      var ctx = this.$refs["swcanvas"].getContext("2d");
      ctx.globalCompositeOperation = "source-over";
      const ridgeCount = this.height / this.ridgeSpacing;
      const ridgeOffset = this.offset % this.ridgeSpacing;
      ctx.fillStyle = this.wheelColor;
      ctx.fillRect(0, 0, this.width, this.height);
      ctx.strokeStyle = this.ridgeColor;
      ctx.lineWidth = 4;
      ctx.shadowColor = this.ridgeColor;
      ctx.shadowOffsetX = 2;
      ctx.shadowOffsetY = ctx.lineWidth - 1;
      ctx.shadowBlur = this.ridgeSpacing / 2;
      const center = {
        x: this.width / 2,
        y: this.height / 2
      };
      for (let i = -1; i <= ridgeCount; i++) {
        const y = i * this.ridgeSpacing + ridgeOffset;
        const ridgeWidth =
          Math.cos((Math.abs(y - center.y) * 1.1) / center.y) *
          (this.width * 0.9);
        const startX = center.x - ridgeWidth / 2;
        const endX = center.x + ridgeWidth / 2;

        ctx.beginPath();
        ctx.moveTo(startX, y);
        ctx.lineTo(endX, y);
        ctx.stroke();
        ctx.closePath();
      }

      // Gradient1
      ctx.globalCompositeOperation = "luminosity";
      var gradient = ctx.createLinearGradient(0, 0, center.x, this.height);
      gradient.addColorStop(0, "rgba(0,0,0,.6)");
      gradient.addColorStop(0.1, "rgba(0,0,0, .3)");
      gradient.addColorStop(0.3, "rgba(0,0,0, 0)");
      gradient.addColorStop(0.7, "rgba(0,0,0, .4)");
      // gradient.addColorStop(0.7, "#444");
      gradient.addColorStop(1, "rgba(0,0,0, .6)");
      ctx.fillStyle = gradient;
      ctx.fillRect(0, 0, this.width, this.height);

      // Gradient2
      ctx.globalCompositeOperation = "luminosity";
      var gradient2 = ctx.createLinearGradient(0, 0, this.width, 0);
      gradient2.addColorStop(0, "rgba(0,0,0,.7)");
      gradient2.addColorStop(0.15, "rgba(0,0,0, .3)");
      gradient2.addColorStop(0.45, "rgba(0,0,0, 0)");
      gradient2.addColorStop(0.8, "rgba(0,0,0, .3)");
      gradient2.addColorStop(0, "rgba(0,0,0,.7)");
      ctx.fillStyle = gradient2;
      ctx.fillRect(0, 0, this.width, this.height);
    }
  }
};
</script>

