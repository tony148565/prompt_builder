<template>
  <div id="app">
    <ToolBar
      @new-session="newSession"
      @save-session="saveSession"
      @load-session="loadSession"
      @clear-workspace="clearWorkspace"
      @export-prompt="exportPrompt"
    />
    <div class="main-layout">
      <ModuleLibrary @add-block="addBlock" />
      <FreeCanvas :blocks="blocks" @select="selectBlock" @update-position="updatePosition" />
      <DetailPanel :block="selectedBlock" @update="updateBlock" />
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
      selectedBlock: null
    }
  },
  methods: {
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
      const text = this.blocks.map(b => `(${b.prompt}:${b.weight})`).join(', ')
      navigator.clipboard.writeText(text)
      alert('已複製 prompt：\n' + text)
    },
    addBlock(block) {
      this.blocks.push({ ...block, x: 100, y: 100, weight: 1.0 })
    },
    selectBlock(block) {
      this.selectedBlock = block
    },
    updateBlock(updated) {
      Object.assign(this.selectedBlock, updated)
    },
    updatePosition(index, x, y) {
      this.blocks[index].x = x
      this.blocks[index].y = y
    }
  }
}
</script>

<style scoped>
#app {
  height: 100vh;
  display: flex;
  flex-direction: column;
}
.main-layout {
  flex: 1;
  display: flex;
  height: 100%; /* ← 這很重要 */
  overflow: hidden;
}
</style>
