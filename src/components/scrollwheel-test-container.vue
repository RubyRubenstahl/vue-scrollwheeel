<template>
  <scroll-wheel
    :width="60"
    :height="200"
    @tick="handleTick"
    :sensitivity="1"
    :debugMode="true"
    :ridgeSpacing="15"
    ridgeColor="red"
    wheelColor="darkred"
  />
</template>
<script>
import ScrollWheel from "./vue-scrollwheel";
export default {
  components: { ScrollWheel },
  mounted() {
    this.handleTick();
  },
  methods: {
    handleTick(e, data) {
      console.log(e);
    }
  }
};
</script>
