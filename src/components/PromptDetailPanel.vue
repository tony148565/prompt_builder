<template>
  <aside class="detail-panel" v-if="block">
    <h3>模組細節</h3>
    <div class="form-group">
      <label>Prompt</label>
      <input type="text" v-model="editableBlock.prompt" />
    </div>
    <div class="form-group">
      <label>權重</label>
      <input type="number" step="0.1" v-model.number="editableBlock.weight" />
    </div>
    <div class="form-group">
      <label>群組（可空白）</label>
      <input type="text" v-model="editableBlock.group" />
    </div>

    <button @click="applyChanges">✔ 套用變更</button>
    <button @click="deleteThis" class="danger">🗑 刪除模組</button>
  </aside>
</template>

<script>
export default {
  name: 'PromptDetailPanel',
  props: ['block'],
  emits: ['update', 'delete-block'],
  data() {
    return {
      editableBlock: null
    }
  },
  watch: {
    block: {
      immediate: true,
      handler(newVal) {
        this.editableBlock = newVal ? { ...newVal } : null
      }
    }
  },
  methods: {
    applyChanges() {
      this.$emit('update', this.editableBlock)
    },
    deleteThis() {
      this.$emit('delete-block', this.block)
    }
  }
}
</script>

<style scoped>
.detail-panel {
  height: 100%;
  width: 250px;
  background-color: var(--panel-bg);
  color: var(--text-color);
  padding: 1em;
  border-left: 1px solid var(--block-border);
  overflow-y: auto;
}
.form-group {
  margin-bottom: 1em;
}
input {
  width: 100%;
  padding: 0.4em;
  border: 1px solid var(--block-border);
  border-radius: 4px;
  font-size: 1em;
  background-color: var(--bg-block);
  color: var(--text-color);
}
button {
  width: 100%;
  padding: 0.5em 1em;
  margin-top: 0.5em;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
button.danger {
  background-color: #c0392b;
}
button.danger:hover {
  background-color: #e74c3c;
}
</style>
