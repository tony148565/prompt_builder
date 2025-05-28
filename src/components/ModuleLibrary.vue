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
  background-color: #f2f2f2;
  padding: 1em;
  overflow-y: auto;
}
.block-list {
  display: flex;
  flex-direction: column;
  gap: 0.5em;
}
.block-item {
  background-color: #ddd;
  padding: 0.5em;
  cursor: pointer;
  border-radius: 4px;
  transition: background 0.2s;
}
.block-item:hover {
  background-color: #ccc;
}
</style>
