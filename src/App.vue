<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import ImageSelectAreas from './components/ImageSelectAreas.vue';

const areas = [
  {
    // left: 380.5,
    // top: 451.1333312988281,
    // startX: 359.5,
    // startY: 521.1333312988281,
    // width: 159,
    // height: 273,
    relativeWidth: 0.2864864864864865,
    relativeHeight: 0.34777070063694265,
    relativeX: 0.6981981981981982,
    relativeY: 0.5874309952851313,
  },
];

const dealWithAdded = stuff => {
  console.log('Added', stuff);
};
</script>

<template>
  <div class="index">
    <h1>Image select areas</h1>
    <em>Click and drag over the image, resize and move areas around.</em>
    <div class="image">
      <image-select-areas
        url="https://picsum.photos/500/800"
        @added="dealWithAdded"
        v-model="areas"
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

.index em {
  margin-bottom: 20px;
}

.image {
  margin: 0px auto;
  max-height: 100vh;
}
</style>
