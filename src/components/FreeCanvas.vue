<template>
  <div class="canvas" @mousedown="startDrag" @mousemove="onDrag" @mouseup="endDrag">
    <div
      v-for="(block, i) in blocks"
      :key="block.id"
      :class="['block', block.type, { selected: block.id === selectedBlock?.id }]"
      :style="blockStyle(block)"
      @mousedown.stop="select(block, $event)"
    >
      <div class="block-content">
        <div v-if="block.type === 'group'">
          <strong>{{ block.groupName }}</strong>
          <small>({{ block.groupType }})</small>
        </div>
        <div v-else>
          {{ block.prompt }} ({{ block.weight }})
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FreeCanvas',
  props: ['blocks', 'selectedBlock'],
  emits: ['select', 'update-position'],
  data() {
    return {
      draggingId: null,
      offsetX: 0,
      offsetY: 0,
      canvasRect: null,
      isDragging: false
    };
  },
  computed: {
    groupBlocks() {
      return this.blocks.filter(b => b.type === 'group');
    }
  },
  methods: {
    blockStyle(block) {
      return {
        left: block.x + 'px',
        top: block.y + 'px',
        width: block.width ? block.width + 'px' : 'auto',
        height: block.height ? block.height + 'px' : 'auto'
      };
    },
    select(block, event) {
      const blockEl = event.currentTarget.getBoundingClientRect();
      this.offsetX = event.clientX - blockEl.left;
      this.offsetY = event.clientY - blockEl.top;
      this.draggingId = block.id;
      this.isDragging = true;
      this.canvasRect = this.$el.getBoundingClientRect();
      this.$emit('select', block);
    },
    startDrag() {},
    onDrag(event) {
      if (this.isDragging && this.draggingId && this.canvasRect) {
        const block = this.blocks.find(b => b.id === this.draggingId);
        if (block) {
          block.x = event.clientX - this.canvasRect.left - this.offsetX;
          block.y = event.clientY - this.canvasRect.top - this.offsetY;
        }
      }
    },
    endDrag() {
      if (!this.draggingId) return;
      const draggedIndex = this.blocks.findIndex(b => b.id === this.draggingId);
      if (draggedIndex === -1) return;
      const draggedBlock = this.blocks[draggedIndex];
      if (!draggedBlock || draggedBlock.type === 'group') {
        this.draggingId = null;
        return;
      }

      let addedToGroup = false;
      for (const group of this.groupBlocks) {
        if (this.isInside(draggedBlock, group)) {
          const exists = group.children.some(c => c.prompt === draggedBlock.prompt);
          if (!exists) {
            group.children.push({ prompt: draggedBlock.prompt }); // ✅ only prompt
            this.blocks.splice(draggedIndex, 1); // ❌ destroy original
            addedToGroup = true;
          }
          break;
        }
      }

      this.draggingId = null;
    },
    isInside(block, group) {
      const padding = 10;
      return (
        block.x + 100 > group.x - padding &&
        block.x < group.x + group.width + padding &&
        block.y + 60 > group.y - padding &&
        block.y < group.y + group.height + padding
      );
    }
  }
};
</script>

<style scoped>
.canvas {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: var(--bg-canvas);
}
.block {
  position: absolute;
  border: 2px solid var(--block-border);
  background-color: var(--bg-block);
  padding: 0.5em;
  border-radius: 6px;
  box-shadow: 2px 2px 5px rgba(0,0,0,0.1);
  cursor: grab;
}
.block.selected {
  border-color: var(--selected-border);
}
.block-content {
  font-size: 0.95em;
  color: var(--text-color);
}
</style>
