<template>
  <aside class="library">
    <h3>模組庫</h3>
    <div class="block-list">
      <div
        class="block-item"
        v-for="(block, i) in modules"
        :key="i"
        @click="$emit('add-block', block)"
      >
        {{ block.prompt }}
      </div>
    </div>
  </aside>
</template>

<script>
export default {
  name: 'ModuleLibrary',
  data() {
    return {
      modules: [
        { type: 'subject', prompt: '1girl' },
        { type: 'style', prompt: 'anime style' },
        { type: 'clothing', prompt: 'school uniform' },
        { type: 'background', prompt: 'cityscape' },
        { type: 'negative', prompt: 'blurry' }
      ]
    }
  }
}
</script>

<style scoped>
.library {
  width: 200px;
  background-color: var(--lib-bg);
  color: var(--text-color);
  padding: 1em;
  height: 100%;
  overflow-y: auto;
}

.block-list {
  display: flex;
  flex-direction: column;
  gap: 0.5em;
}

.block-item {
  background-color: var(--bg-block);
  border: 1px solid var(--block-border);
  color: var(--text-color);
  padding: 0.5em;
  cursor: pointer;
  border-radius: 4px;
  text-align: center;
  font-weight: 500;
  transition: background 0.2s, border 0.2s;
}

.block-item:hover {
  background-color: var(--hover-bg);
  border: 1px solid var(--block-border); /* ← hover 時也保留邊框 */
}
</style>

