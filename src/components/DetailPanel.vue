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
    <button @click="applyChanges">✔ 套用變更</button>
  </aside>
</template>

<script>
export default {
  name: 'DetailPanel',
  props: ['block'],
  emits: ['update'],
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
    }
  }
}
</script>

<style scoped>
.detail-panel {
  width: 250px;
  background-color: #f9f9f9;
  padding: 1em;
  border-left: 1px solid #ccc;
}
.form-group {
  margin-bottom: 1em;
}
input[type="text"],
input[type="number"] {
  width: 100%;
  padding: 0.4em;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1em;
}
button {
  padding: 0.5em 1em;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
</style>
