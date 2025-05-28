<template>
  <div class="canvas" @mousedown="startDrag" @mousemove="onDrag" @mouseup="stopDrag">
    <div
      class="block"
      v-for="(block, index) in blocks"
      :key="index"
      :style="{
        left: block.x + 'px',
        top: block.y + 'px'
      }"
      @mousedown.stop="selectBlock(index, $event)"
    >
      {{ block.prompt }} ({{ block.weight }})
    </div>
  </div>
</template>

<script>
export default {
  name: 'FreeCanvas',
  props: ['blocks'],
  emits: ['select', 'update-position'],
  data() {
    return {
      draggingIndex: null,
      offsetX: 0,
      offsetY: 0
    }
  },
  methods: {
    selectBlock(index, event) {
      this.draggingIndex = index;
      const block = this.blocks[index];
      this.offsetX = event.clientX - block.x;
      this.offsetY = event.clientY - block.y;
      this.$emit('select', block);
    },
    startDrag(event) {
      // optional: detect deselection here
    },
    onDrag(event) {
      if (this.draggingIndex !== null) {
        const x = event.clientX - this.offsetX;
        const y = event.clientY - this.offsetY;
        this.$emit('update-position', this.draggingIndex, x, y);
      }
    },
    stopDrag() {
      this.draggingIndex = null;
    }
  }
}
</script>

<style scoped>
.canvas {
  flex: 1;
  position: relative;
  background-color: #ffffff;
  border: 1px solid #ccc;
  overflow: hidden;
}
.block {
  position: absolute;
  background-color: #e0e0e0;
  border: 1px solid #aaa;
  padding: 0.5em;
  cursor: move;
  border-radius: 4px;
  user-select: none;
  transition: box-shadow 0.2s;
}
.block:hover {
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
}
</style>
