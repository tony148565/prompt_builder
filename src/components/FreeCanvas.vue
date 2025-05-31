<template>
  <div class="canvas" @mousedown="startDrag" @mousemove="onDrag" @mouseup="endDrag">
    <!-- Merge Groups -->
    <div
      v-for="group in mergeGroups"
      :key="group.id"
      :class="['block', 'group', 'merge', { selected: group.id === selectedBlock?.id }]"
      :style="blockStyle(group)"
      @mousedown.stop="select(group, $event)"
    >
      <div class="block-content">
        <strong>{{ group.groupName }}</strong>
      </div>
    </div>

    <!-- Logic Groups -->
    <div
      v-for="group in logicGroups"
      :key="group.id"
      :class="['block', 'group', 'logic', { selected: group.id === selectedBlock?.id }]"
      :style="blockStyle(group)"
      @mousedown.stop="select(group, $event)"
    >
      <div class="block-content">
        <strong>{{ group.groupName }}</strong>
        <div
          v-if="group.children?.length"
          v-for="child in group.children"
          :key="child.id"
          class="embedded-block"
        >
          {{ findBlock(child.id)?.prompt || '(未知)' }}
        </div>
      </div>
    </div>

    <!-- Normal Prompt Blocks -->
    <div
      v-for="block in promptBlocks"
      :key="block.id"
      :class="['block', { selected: block.id === selectedBlock?.id }]"
      :style="blockStyle(block)"
      @mousedown.stop="select(block, $event)"
    >
      <div class="block-content">
        {{ block.prompt }} ({{ block.weight }})
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
    mergeGroups() {
      return this.blocks.filter(b => b.type === 'group' && b.groupType === 'merge');
    },
    logicGroups() {
      return this.blocks.filter(b => b.type === 'group' && b.groupType === 'logic');
    },
    promptBlocks() {
      const logicChildrenIds = this.logicGroups.flatMap(g => g.children?.map(c => c.id) || []);
      return this.blocks.filter(b => b.type !== 'group' && !logicChildrenIds.includes(b.id));
    }
  },
  methods: {
    blockStyle(block) {
      const basePadding = 40;
      const charWidth = 8;
      const minWidth = 80;
      const maxWidth = 320;

      const lineHeight = 22;
      const maxCharsPerLine = 24;
      const minHeight = 50;
      const maxHeight = 180;

      let width = block.width || 120;
      let height = block.height || 'auto';

      if (block.type === 'group' && block.groupType === 'merge') {
        const estimatedWidth = block.groupName.length * charWidth + basePadding;
        width = Math.min(Math.max(estimatedWidth, minWidth), maxWidth);
        const numLines = Math.ceil(block.groupName.length / maxCharsPerLine);
        const estimatedHeight = numLines * lineHeight;
        height = Math.min(Math.max(estimatedHeight, minHeight), maxHeight);
      }

      if (block.type === 'group' && block.groupType === 'logic') {
        width = block.width || 280;
        height = block.height || 160;
      }

      return {
        left: block.x + 'px',
        top: block.y + 'px',
        width: width + 'px',
        height: typeof height === 'number' ? height + 'px' : height
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

      // Handle Merge Groups
      for (const group of this.mergeGroups) {
        if (this.isInside(draggedBlock, group)) {
          const exists = group.children.some(c => c.prompt === draggedBlock.prompt);
          if (!exists) {
            group.children.push({ prompt: draggedBlock.prompt });
            group.groupName = group.children.map(c => c.prompt).join(' ');
            this.blocks.splice(draggedIndex, 1);
          }
          this.draggingId = null;
          return;
        }
      }

      // Handle Logic Groups
      for (const group of this.logicGroups) {
        if (this.isInside(draggedBlock, group)) {
          if (!group.children) this.$set(group, 'children', []);
          const exists = group.children.some(c => c.id === draggedBlock.id);
          if (!exists) {
            group.children.push({ id: draggedBlock.id });
            // 不再 splice block，改由 computed 的 promptBlocks 過濾顯示
          }
          this.draggingId = null;
          return;
        }
      }

      this.draggingId = null;
    },
    isInside(block, group) {
      const padding = 10;
      const gw = group.width || 280;
      const gh = group.height || 160;
      return (
        block.x + 100 > group.x - padding &&
        block.x < group.x + gw + padding &&
        block.y + 60 > group.y - padding &&
        block.y < group.y + gh + padding
      );
    },
    findBlock(id) {
      return this.blocks.find(b => b.id === id);
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
  color: var(--text-color);
}
.block.selected {
  border-color: var(--selected-border);
}
.block-content {
  word-break: break-word;
  white-space: normal;
}
.embedded-block {
  font-size: 12px;
  margin-left: 10px;
  margin-top: 4px;
  padding-left: 6px;
  border-left: 2px dotted #aaa;
  color: var(--text-color);
}
</style>
