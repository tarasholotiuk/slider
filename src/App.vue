<template>
  <div id="app">
    <Gallery></Gallery>
  </div>
</template>

<script>

import Gallery from "@/components/gallery";
export default {
  name: 'App',
  components: {
    Gallery
  }
}
</script>

<style>
#app {
  position: relative;
  margin: auto;
  width: 1920px;
  height: 1200px;
  box-shadow: 0 0 10px gray;
}
</style>
