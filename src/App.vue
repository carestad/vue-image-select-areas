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
const output = ref('');

const onAdded = (newArea) => {
  newArea.label = 'Test';
};
const onEditAreaLabel = index => {
  const area = areas.value[index];
  area.label = prompt('Label?', area.label ?? '');
};
const onDeleteArea = index => {
  areas.value.splice(index, 1);
};
const clearAreas = () => {
  areas.value = [];
};

const saveAreas = () => {
  output.value = JSON.stringify(areas.value, undefined, 2);
};
</script>

<template>
  <div class="index">
    <h1>Image select areas</h1>
    <em>Click and drag over the image, resize and move areas around.</em>
    <div class="image">
      <image-select-areas
        url="https://dummyimage.com/500x800/fff/aaa"
        @added="onAdded"
        v-model="areas"
      >
        <template #default="{area}">
          <div>
            <div>{{area.label ?? 'Default slot'}}</div>
            <div>Position: {{area.left}} / {{area.top}}</div>
            <div>Size: {{Math.round(area.width)}}x{{Math.round(area.height)}}px</div>
          </div>
        </template>
        <template #toolbar="{index}">
          <div class="toolbar">
            <button class="edit" @click="onEditAreaLabel(index)" title="Edit label">🏷</button>
            <button class="delete" @click="onDeleteArea(index)" title="Remove">🗑</button>
          </div>
        </template>
      </image-select-areas>
    </div>
    <div class="toolbar">
      <button class="clear" @click="clearAreas">Clear</button>
      <button class="save" @click="saveAreas">Show output</button>
    </div>
    <pre v-if="output" style="text-align: start">{{output}}</pre>
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
.image .toolbar {
  position: absolute;
  top: 5px;
  right: 5px;
}
.image .toolbar * {
  margin-left: 5px;
}
</style>
