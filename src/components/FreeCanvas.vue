<template>
  <div class="canvas" @mousedown="startDrag" @mousemove="onDrag" @mouseup="stopDrag">
    <!-- 顯示所有區塊 -->
    <div
      class="block"
      v-for="(block, index) in blocks"
      :key="index"
      :style="{ left: block.x + 'px', top: block.y + 'px', width: block.width + 'px', height: block.height + 'px' }"
      :class="{
        selected: index === draggingIndex,
        group: block.type === 'group',
        'merge-group': block.groupType === 'merge',
        'logic-group': block.groupType === 'logic'
      }"
      @mousedown.stop="selectBlock(index, $event)"
    >
      <div v-if="block.type === 'group'" class="group-label">{{ block.groupName }}</div>
      <div v-else>{{ block.prompt }} ({{ block.weight }})</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FreeCanvas',
  props: ['blocks'],
  emits: ['select', 'update-position', 'update-group'],
  data() {
    return {
      draggingIndex: null,
      offsetX: 0,
      offsetY: 0
    }
  },
  methods: {
    selectBlock(index, event) {
      this.draggingIndex = index
      const block = this.blocks[index]
      this.offsetX = event.clientX - block.x
      this.offsetY = event.clientY - block.y
      this.$emit('select', block)
    },
    startDrag(event) {},
    onDrag(event) {
      if (this.draggingIndex !== null) {
        const x = event.clientX - this.offsetX
        const y = event.clientY - this.offsetY
        this.$emit('update-position', this.draggingIndex, x, y)
      }
    },
    stopDrag() {
      if (this.draggingIndex === null) return

      const dragged = this.blocks[this.draggingIndex]

      // 如果不是 group 自己，才進行檢查
      if (dragged.type !== 'group') {
        this.blocks.forEach((block, i) => {
          if (block.type === 'group') {
            const withinX = dragged.x > block.x && dragged.x < block.x + block.width
            const withinY = dragged.y > block.y && dragged.y < block.y + block.height
            if (withinX && withinY) {
              // 加入 group.children，如果尚未包含
              if (!block.children.includes(this.draggingIndex)) {
                block.children.push(this.draggingIndex)
                this.$emit('update-group', block.id, block.children)
              }
            }
          }
        })
      }

      this.draggingIndex = null
    }
  }
}
</script>

<style scoped>
.canvas {
  flex: 1;
  position: relative;
  overflow: hidden;
  background-color: var(--bg-canvas);
}

/* 基本區塊樣式 */
.block {
  position: absolute;
  background-color: var(--bg-block);
  color: var(--text-color);
  border: 1px solid var(--block-border);
  border-radius: 4px;
  cursor: move;
  padding: 0.4em;
  box-sizing: border-box;
}

/* 群組區塊特殊樣式 */
.block.group {
  background-color: transparent;
  border: 2px dashed var(--block-border);
  display: flex;
  align-items: flex-start;
  justify-content: center;
  pointer-events: auto;
}

.block.group .group-label {
  font-weight: bold;
  font-size: 0.9em;
  padding: 0.2em 0.4em;
  background-color: var(--bg-block);
  border-radius: 4px;
}

/* 顏色提示 */
.merge-group {
  border-color: #c62828;
}
.logic-group {
  border-color: #2e7d32;
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
