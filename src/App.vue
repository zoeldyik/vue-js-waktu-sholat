<template>
  <div id="app" class="pb-4 pt-3 pt-md-5">
    <control-app @get-jadwal="openDisplay($event)"></control-app>
    <jadwal-component v-if="data" :data="data"></jadwal-component>
  </div>
</template>

<script>
import control from "./components/control";
import displayJadwalHarian from "./components/display-jadwal-harian";

export default {
  name: "App",
  components: {
    "jadwal-component": displayJadwalHarian,
    "control-app": control,
  },
  data() {
    return {
      data: null,
    };
  },
  methods: {
    openDisplay(obj) {
      this.data = obj;
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;800&display=swap");

#app {
  font-family: "Nunito", sans-serif;
  background-color: whitesmoke;
  min-height: 100vh;
  box-sizing: border-box;
}
</style>
