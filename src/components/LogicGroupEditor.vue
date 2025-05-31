<template>
  <div class="form-group">
    <label>包含的模組</label>
    <ul>
      <li v-for="(child, index) in modelValue" :key="child.id" class="child-item">
        {{ child.prompt || '(未知)' }} ({{ child.weight ?? 1.0 }})
        <div class="controls">
          <button @click="moveUp(index)" :disabled="index === 0">⬆️</button>
          <button @click="moveDown(index)" :disabled="index === modelValue.length - 1">⬇️</button>
          <button class="remove-btn" @click="removeChild(index)">❌</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'LogicGroupEditor',
  props: ['modelValue'],
  emits: ['update:modelValue', 'update', 'restore-block'],
  methods: {
    removeChild(index) {
      const removed = this.modelValue.splice(index, 1)[0];
      this.$emit('update:modelValue', this.modelValue);
      this.$emit('update');

      // 確保還原資料完整
      if (removed?.id && removed?.prompt) {
        this.$emit('restore-block', {
          id: removed.id,
          prompt: removed.prompt,
          weight: removed.weight ?? 1.0,
          type: 'prompt'
        });
      }
    },
    moveUp(index) {
      if (index <= 0) return;
      const tmp = this.modelValue[index];
      this.modelValue.splice(index, 1);
      this.modelValue.splice(index - 1, 0, tmp);
      this.$emit('update:modelValue', this.modelValue);
      this.$emit('update');
    },
    moveDown(index) {
      if (index >= this.modelValue.length - 1) return;
      const tmp = this.modelValue[index];
      this.modelValue.splice(index, 1);
      this.modelValue.splice(index + 1, 0, tmp);
      this.$emit('update:modelValue', this.modelValue);
      this.$emit('update');
    }
  }
};
</script>

<style scoped>
ul {
  padding-left: 0;
  list-style: none;
}
li.child-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.5em;
}
.controls {
  display: flex;
  gap: 0.3em;
}
button {
  padding: 0.3em 0.5em;
  border: none;
  border-radius: 4px;
  font-size: 0.8em;
  cursor: pointer;
}
.remove-btn {
  background-color: #c0392b;
  color: white;
}
.remove-btn:hover {
  background-color: #e74c3c;
}
button:disabled {
  opacity: 0.5;
  cursor: default;
}
</style>
