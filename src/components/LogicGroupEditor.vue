<template>
  <div class="form-group">
    <label>包含的模組</label>
    <ul>
      <li v-for="(child, index) in modelValue" :key="child.id">
        {{ findBlock(child.id)?.prompt || '(未知)' }}
        <button class="remove-btn" @click="removeChild(index)">❌</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'LogicGroupEditor',
  props: ['modelValue', 'allBlocks'],
  emits: ['update:modelValue', 'update'],
  methods: {
    findBlock(id) {
      return this.allBlocks.find(b => b.id === id);
    },
    removeChild(index) {
      this.modelValue.splice(index, 1);
      this.$emit('update:modelValue', this.modelValue);
      this.$emit('update');
    },
    onBlockRemoved(blockId) {
      const index = this.modelValue.findIndex(c => c.id === blockId);
      if (index !== -1) {
        this.removeChild(index);
      }
    }
  }
};
</script>

<style scoped>
ul {
  padding-left: 0;
  list-style: none;
}
li {
  margin-bottom: 0.5em;
}
button {
  padding: 0.3em 0.5em;
  background-color: #c0392b;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.8em;
}
button:hover {
  background-color: #e74c3c;
}
</style>
