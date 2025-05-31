<template>
  <div class="form-group">
    <label>包含的模組</label>
    <ul>
      <li v-for="(child, index) in localChildren" :key="child.id" class="child-item">
        {{ child.prompt }} ({{ child.weight ?? 1.0 }})
        <div class="controls">
          <button @click="moveUp(index)" :disabled="index === 0">⬆️</button>
          <button @click="moveDown(index)" :disabled="index === localChildren.length - 1">⬇️</button>
          <button class="remove-btn" @click="removeChild(index)">❌</button>
        </div>
      </li>
    </ul>

    <div class="form-group">
      <label>合併預覽</label>
      <p style="font-weight: bold">{{ mergedPrompt }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MergeGroupEditor',
  props: ['modelValue'],
  emits: ['update:modelValue', 'update', 'restore-block'],  // ✅ 補上這行
  computed: {
    localChildren: {
      get() {
        return this.modelValue;
      },
      set(val) {
        this.$emit('update:modelValue', val);
      }
    },
    mergedPrompt() {
      return this.modelValue.map(c => c.prompt).join(' ');
    }
  },
  methods: {
    emitUpdate() {
      this.$emit('update');
    },
    moveUp(index) {
      const arr = [...this.localChildren];
      const tmp = arr[index];
      arr.splice(index, 1);
      arr.splice(index - 1, 0, tmp);
      this.localChildren = arr;
      this.emitUpdate();
    },
    moveDown(index) {
      const arr = [...this.localChildren];
      const tmp = arr[index];
      arr.splice(index, 1);
      arr.splice(index + 1, 0, tmp);
      this.localChildren = arr;
      this.emitUpdate();
    },
    removeChild(index) {
      const arr = [...this.localChildren];
      const removed = arr.splice(index, 1)[0];
      this.localChildren = arr;
      this.emitUpdate(); // ✅ 更新本地 + 通知上層更新
      this.$emit('restore-block', removed); // ✅ 發射給 GroupDetailPanel
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
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #2a2a2a;
  padding: 0.5em;
  border-radius: 4px;
}
.controls button {
  padding: 0.3em 0.5em;
  margin-left: 0.2em;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.8em;
}
.controls button:hover {
  background-color: #45a049;
}
.controls .remove-btn {
  background-color: #c0392b;
}
.controls .remove-btn:hover {
  background-color: #e74c3c;
}
</style>
