<template>
  <div id="app">
    <ToolBar
      @new-session="newSession"
      @save-session="saveSession"
      @load-session="loadSession"
      @clear-workspace="clearWorkspace"
      @export-prompt="exportPrompt"
      @toggle-theme="toggleTheme"
    />
    <div class="main-layout">
      <ModuleLibrary @add-block="addBlock" />
      <FreeCanvas :blocks="blocks" @select="selectBlock" @update-position="updatePosition" />
      <DetailPanel
        :block="selectedBlock"
        :all-blocks="blocks"
        @update="updateBlock"
        @delete-block="deleteBlock"
        @remove-child="onRemoveChild"
        @restore-block="restoreToCanvas" 
      />

    </div>
  </div>
</template>

<script>
import ToolBar from './components/ToolBar.vue'
import ModuleLibrary from './components/ModuleLibrary.vue'
import FreeCanvas from './components/FreeCanvas.vue'
import DetailPanel from './components/DetailPanel.vue'

export default {
  components: { ToolBar, ModuleLibrary, FreeCanvas, DetailPanel },
  data() {
    return {
      blocks: [],
      selectedBlock: null,
      theme: 'light'
    }
  },
  mounted() {
    const saved = localStorage.getItem('theme') || 'light'
    this.theme = saved
    document.body.className = saved
  },
  methods: {
    toggleTheme() {
      this.theme = this.theme === 'light' ? 'dark' : 'light'
      document.body.className = this.theme
      localStorage.setItem('theme', this.theme)
    },
    newSession() {
      this.blocks = []
      this.selectedBlock = null
    },
    saveSession() {
      const data = JSON.stringify(this.blocks)
      localStorage.setItem('promptSession', data)
      alert('工作階段已儲存')
    },
    loadSession() {
      const data = localStorage.getItem('promptSession')
      if (data) {
        this.blocks = JSON.parse(data)
        alert('已載入工作階段')
      }
    },
    clearWorkspace() {
      this.blocks = []
    },
    exportPrompt() {
      const text = this.blocks
        .filter(b => b.type !== 'group')
        .map(b => `(${b.prompt}:${b.weight})`)
        .join(', ')
      navigator.clipboard.writeText(text)
      alert('已複製 prompt：\n' + text)
    },
    addBlock(block) {
      if (block.type === 'group') {
        const group = {
          ...block,
          id: 'group-' + Date.now(),
          x: block.x || 100,
          y: block.y || 100,
          width: block.width || 200,
          height: block.height || 120,
          children: [],
          groupName: block.groupName || '未命名群組',
          groupWeight: 1.0  // ← 加入這行
        };
        this.blocks.push(group);
      } else {
        this.blocks.push({ ...block, x: 100, y: 100, weight: 1.0 });
      }
    },
    selectBlock(block) {
      this.selectedBlock = block
    },
    updateBlock(updated) {
      Object.assign(this.selectedBlock, updated)
    },
    deleteBlock(block) {
      this.blocks = this.blocks.filter(b => b !== block)
      this.selectedBlock = null
    },
    updatePosition(index, x, y) {
      this.blocks[index].x = x
      this.blocks[index].y = y
    },
    onRemoveChild(prompt) {
      const exists = this.blocks.some(b => b.type === 'prompt' && b.prompt === prompt);
      if (!exists) {
        this.blocks.push({
          id: 'prompt-' + Date.now(),
          prompt,
          type: 'prompt',
          x: 100,
          y: 100,
          weight: 1.0
        });
      }
    },
    restoreToCanvas(block) {
      const exists = this.blocks.some(b => b.id === block.id);
      if (!exists) {
        this.blocks.push({
          id: block.id,
          prompt: block.prompt,
          type: 'prompt',
          x: 100,
          y: 100,
          weight: block.weight ?? 1.0
        });
      }
    }



  }
}
</script>

<style scoped>
.main-layout {
  flex: 1;
  height: 100%;
  display: flex;
  overflow: hidden;
}
.main-layout > *:nth-child(1) {
  width: 220px;
  min-width: 200px;
}
.main-layout > *:nth-child(2) {
  flex: 1;
}
.main-layout > *:nth-child(3) {
  width: 260px;
  min-width: 240px;
  border-left: 1px solid var(--block-border);
}
</style>