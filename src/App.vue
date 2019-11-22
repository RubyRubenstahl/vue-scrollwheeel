<template>
  <div id="app">
    <h1>Vue Scrollwheel Demo</h1>
    <div class="container">
      <div class="controls">
        <label>
          Wheel color
          <input type="text" v-model="wheelColor" />
        </label>
        <label>
          Ridge color
          <input type="text" v-model="ridgeColor" />
        </label>
        <label>
          Sensitivity
          <input type="number" v-model="sensitivity" />
        </label>
      </div>
      <scroll-wheel
        :width="60"
        :height="200"
        @tick="handleTick"
        :sensitivity="sensitivity"
        :debugMode="true"
        :ridgeSpacing="15"
        :ridgeColor="ridgeColor"
        :wheelColor="wheelColor"
        v-model="level"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  components: {},
  data() {
    return {
      ridgeColor: "black",
      wheelColor: "white",
      sensitivity: 1,
      level: 50
    };
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

.container {
  display: flex;
  width: 100%;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

input {
  width: 100px;
}

label {
  display: block;
}

.controls {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  justify-content: flex-end;
  margin-bottom: 16px;
}
</style>
