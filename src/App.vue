<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import ImageSelectAreas from './components/ImageSelectAreas.vue';

const areas = [
  {
    left: 390.5, 
    top: 181.13333129882812,
    width: 155,
    height: 276,
    relativeWidth: 0.27927927927927926,
    relativeHeight: 0.35159235668789807,
    relativeX: 0.9882882882882883,
    relativeY: 0.5861571099348128,
  }
];
</script>

<template>
  <div class="index">
    <h1>Her skal det tagges!</h1>
    <div class="image">
      <image-select-areas 
        url="https://s3.kupp.no/staging/rema-1000/2022/27/nordreostland-8.jpg"
        :existing-areas="areas"
      />
    </div>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.index {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.image {
  margin: 0px auto;
  width: 555px;
}
</style>
