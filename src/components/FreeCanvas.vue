<template>
  <div class="canvas" @mousedown="startDrag" @mousemove="onDrag" @mouseup="stopDrag">
        <div
        class="block"
        v-for="(block, index) in blocks"
        :key="index"
        :style="{ left: block.x + 'px', top: block.y + 'px' }"
        :class="{ selected: index === draggingIndex }"
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
  height: 100%;
  position: relative;
  overflow: hidden;
  background-color: var(--bg-canvas);
}

.block {
  background-color: var(--bg-block);
  color: var(--text-color);
  border: 1px solid var(--block-border);
  position: absolute;
  left: 0;
  top: 0;
  cursor: move;
  user-select: none;
}

.block.selected {
  border: 2px solid var(--selected-border);
}

.block:hover {
  box-shadow: 2px 2px 12px rgba(0, 0, 0, 0.6);
}

.block:active {
  cursor: grabbing;
  opacity: 0.85;
  transform: scale(1.02);
}


</style>
