<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import ImageSelectAreas from './components/ImageSelectAreas.vue';
import {ref} from 'vue';

const areas = ref([
  {
    // Only the relative values are required in the objects
    relativeWidth: 0.2864864864864865,
    relativeHeight: 0.34777070063694265,
    relativeX: 0.6981981981981982,
    relativeY: 0.5874309952851313,
  },
]);

const dealWithAdded = stuff => {
  console.log('Added', stuff);
};
function clearAreas() {
  areas.value = [];
};
</script>

<template>
  <div class="index">
    <h1>Image select areas</h1>
    <em>Click and drag over the image, resize and move areas around.</em>
    <div class="image">
      <image-select-areas
        url="https://dummyimage.com/500x800/fff/aaa"
        @added="dealWithAdded"
        v-model="areas"
      >
        <template #default="area">
          <div>
            <div>Default slot</div>
            <div>Position: {{area.left}} / {{area.top}}</div>
            <div>Size: {{Math.round(area.width)}}x{{Math.round(area.height)}}px</div>
          </div>
        </template>
      </image-select-areas>
    </div>
    <div class="toolbar">
      <button class="clear" @click="clearAreas">Clear</button>
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
  max-height: 100vh;
}
</style>
