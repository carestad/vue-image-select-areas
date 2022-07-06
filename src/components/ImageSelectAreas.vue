<template>
  <div class="root" :style="rootStyle">
    <img
      :src="url"
      ref="image"
      :style="imageStyles"
      :width="width"
      :height="height"
    />
    <div 
      ref="overlay" 
      :style="overlayStyles"
      @mousedown="onMouseDown"
      @mousemove="onMouseMoveDebounced"
      @mouseup="onMouseUp"
    >
    </div>
    <div v-if="isCurrentlyDrawing" :style="currentlyDrawingStyles"></div>
    <div 
      v-for="(area, index) in areas" 
      :key="`area-${index}`" 
      :class="['area', {selected: currentlySelectedIndex === index}]" 
      :style="areaStyles(area)" 
      @click="onSelectArea(area, index)"
      @mousedown="onAreaMouseDown($event, area, index)"
      @mousemove="onAreaMoveDebounced($event, area, index)"
      @mouseup="onAreaMouseUp($event, area, index)"
    />
  </div>
</template>

<script>
// https://stackoverflow.com/a/65081210/1223692
/**
 * @param {function} func 
 * @param {number} wait 
 * @param {boolean} immediate 
 */
const debounce = (func, wait, immediate) => {
  var timeout;
  return function() {
      var context = this, args = arguments;
      var later = function() {
          timeout = null;
          if (!immediate) func.apply(context, args);
      };
      var callNow = immediate && !timeout;
      clearTimeout(timeout);
      timeout = setTimeout(later, wait);
      if (callNow) func.apply(context, args);
  };
};

const relativeToAbsolutes = (input, image) => {
  return {
    ...input,
    x: image.width*input.relativeX,
    y: image.height*input.relativeY,
    width: image.width*input.relativeWidth,
    height: image.height*input.relativeHeight,
  };
};

export default {
  name: 'image-select-areas',

  props: {
    url: {
      type: String,
      required: true,
    },
    width: {
      type: [String, Number],
      default: '100%',
    },
    height: {
      type: [String, Number],
      default: '100%',
    },
    existingAreas: {
      type: Array,
      default: () => [],
    },
  },

  data: () => ({
    areas: [],
    currentlySelectedIndex: null,
    isCurrentlyDrawing: false,
    isCurrentlyMoving: false,
    currentlyDrawing: {},
    currentlyMoving: {
      startX: null,
      startY: null,
    },
  }),

  computed: {
    rootStyle() {
      return {
        position: 'relative',
      };
    },
    styles() {
      return {
        width: this.width,
        height: this.height,
      };
    },
    imageStyles() {
      return {
        width: this.width,
        height: this.height,
      };
    },
    overlayStyles() {
      return {
        ...this.styles,
        position: 'absolute',
        top: 0,
        left: 0,
        cursor: 'crosshair',
        zIndex: this.isCurrentlyDrawing ? 5 : 2,
        backgroundColor: 'rgba(0, 0, 0, 0.5)',
      };
    },
    currentlyDrawingStyles() {
      return {
        ...this.areaStyles(this.currentlyDrawing),
        backgroundColor: 'blue',
      };
    },
  },

  mounted() {
    this.areas = this.existingAreas.map(area => this.computeExistingAreaSizes(area));
    console.log('Mounted', this.existingAreas);
  },
  created() {
    this.resetCurrentlyDrawing();
    this.onMouseMoveDebounced = debounce(this.onMouseMove, 10);
    this.onAreaMoveDebounced = debounce(this.onAreaMouseMove, 10);
  },

  methods: {
    areaStyles(area) {
      // console.log('Get area style', area);

      return {
        position: 'absolute',
        top: `${area.top}px`,
        left: `${area.left}px`,
        border: '1px dashed gray',
        backgroundColor: 'green',
        zIndex: this.isCurrentlyMoving ? 5 : 3,
        width: `${area.width}px`,
        height: `${area.height}px`,
        opacity: 0.5,
      };
    },
    computeExistingAreaSizes(area) {
      const image = this.$refs.image;
      console.log('Compute', area);
      return area;
      // return {
      //   ...area,
      //   top: image.naturalWidth*area.relativeX,
      //   left: image.naturalHeight*area.relativeY,
      //   width: image.naturalWidth*area.relativeWidth,
      //   height: image.naturalHeight*area.relativeHeight,
      // };
    },
    resetCurrentlyDrawing() {
      this.currentlyDrawing = {
        left: null,
        top: null,
        startX: null,
        startY: null,
        width: null,
        height: null,
        relativeWidth: null,
        relativeHeight: null,
        relativeX: null,
        relativeY: null,
      };
    },

    /**
     * AREA DRAWING
     */
    onMouseDown(event) {
      const bounding = event.target.getBoundingClientRect();
      const x = event.clientX - bounding.left;
      const y = event.clientY - bounding.top;

      // If we're already drawing it could be a sign that the mouse has gone outside the overlay. In that case we can
      // just endit when we return and click again.
      if (this.isCurrentlyDrawing) {
        this.isCurrentlyDrawing = false;
        return;
      }

      this.currentlyDrawing.left = this.currentlyDrawing.startX = x;
      this.currentlyDrawing.top = this.currentlyDrawing.startY = y;

      this.isCurrentlyDrawing = true;
      
      console.log(`[onMouseDown] At ${x}x${y}`);
    },
    onMouseUp(event) {
      const image = this.$refs.image;
      const bounding = event.target.getBoundingClientRect();
      const x = event.clientX - bounding.left;
      const y = event.clientY - bounding.top;

      this.currentlyDrawing.relativeWidth = this.currentlyDrawing.width/image.width;
      this.currentlyDrawing.relativeHeight = this.currentlyDrawing.height/image.height;
      this.currentlyDrawing.relativeX = x/image.width;
      this.currentlyDrawing.relativeY = y/image.height;

      this.isCurrentlyDrawing = false;
      this.areas.push(this.currentlyDrawing);
      this.resetCurrentlyDrawing();

      console.log(`[onMouseUp] At ${x}x${y}`);
    },
    onMouseMove(event) {
      if (!this.isCurrentlyDrawing) {
        return;
      }

      const bounding = event.target.getBoundingClientRect();
      const imageX = event.clientX - bounding.left;
      const imageY = event.clientY - bounding.top;
      const padding = 3;

      // Width of current selection, when regular left to right
      const width = imageX-this.currentlyDrawing.startX;
      const height = imageY-this.currentlyDrawing.startY;

      this.currentlyDrawing.width = width-padding;
      this.currentlyDrawing.height = height-padding;

      // console.log(`[onMouseMove] size ${width}x${height} | x: ${imageX}, y: ${imageY}`);

      if (imageY < this.currentlyDrawing.startY) {
        // console.log(`[onMouseMove] Moving bottom to top!`, bounding);
        this.currentlyDrawing.top = imageY+padding;
        this.currentlyDrawing.height = this.currentlyDrawing.startY-imageY;
      }

      if (imageX < this.currentlyDrawing.startX) {
        // console.log(`[onMouseMove] Moving left to right!`, bounding);
        this.currentlyDrawing.left = imageX+padding;
        this.currentlyDrawing.width = this.currentlyDrawing.startX-imageX;
      }
    },

     /**
      * AREA REORGANIZING
      */
    onSelectArea(area, index) {
      console.log(`[onSelectArea] Hellu`, area);

      if (this.currentlySelectedIndex === index) {
        this.currentlySelectedIndex = null;
        return;
      }

      this.currentlySelectedIndex = index;
    },
    onAreaMouseDown(event, area, index) {
      if (this.currentlySelectedIndex !== index) {
        return;
      }

      if (this.isCurrentlyMoving) {
        this.isCurrentlyMoving = false;
        this.currentlyMoving.startX = null;
        this.currentlyMoving.startY = null;
        return;
      }

      const bounding = event.target.getBoundingClientRect();
      const x = event.clientX - bounding.left;
      const y = event.clientY - bounding.top;

      this.isCurrentlyMoving = true;
      this.currentlyMoving.startX = x;
      this.currentlyMoving.startY = y;
    },
    onAreaMouseUp(event, area, index) {
      if (this.currentlySelectedIndex !== index) {
        return;
      }

      this.isCurrentlyMoving = false;
      this.currentlyMoving.startX = null;
      this.currentlyMoving.startY = null;
    },
    onAreaMouseMove(event, area, index) {
      if (this.currentlySelectedIndex !== index || !this.isCurrentlyMoving) {
        return;
      }

      const bounding = event.target.getBoundingClientRect();
      const x = event.clientX - bounding.left;
      const y = event.clientY - bounding.top;

      // Calculate diff in left/top/bottom/right when moving
      const diffX = this.currentlyMoving.startX-x;
      const diffY = this.currentlyMoving.startY-y;

      area.top -= diffY;
      area.left -= diffX;

      // console.log(`[onAreaMouseMove] diff: ${diffX} / ${diffY}`);
    },
  },
};
</script>

<style scoped>
.area.selected {
  border: 2px dashed black !important;
  cursor: pointer;
}
</style>
