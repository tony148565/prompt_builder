<template>
  <div class="form-group">
    <label>包含的模組</label>
    <ul>
      <li v-for="(child, index) in localChildren" :key="child.prompt">
        <input
          type="text"
          v-model="child.prompt"
          @input="emitUpdate"
        />
        <button @click="moveUp(index)" :disabled="index === 0">⬆️</button>
        <button @click="moveDown(index)" :disabled="index === localChildren.length - 1">⬇️</button>
        <button class="remove-btn" @click="removeChild(index)">❌</button>
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
  emits: ['update:modelValue', 'update'],
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
      const tmp = this.localChildren[index];
      this.localChildren.splice(index, 1);
      this.localChildren.splice(index - 1, 0, tmp);
      this.emitUpdate();
    },
    moveDown(index) {
      const tmp = this.localChildren[index];
      this.localChildren.splice(index, 1);
      this.localChildren.splice(index + 1, 0, tmp);
      this.emitUpdate();
    },
    removeChild(index) {
      this.localChildren.splice(index, 1);
      this.emitUpdate();
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
input[type="text"] {
  width: 100%;
  padding: 0.4em;
  margin-bottom: 0.2em;
}
button {
  padding: 0.3em 0.5em;
  margin-right: 0.2em;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.8em;
}
button:hover {
  background-color: #45a049;
}
.remove-btn {
  background-color: #c0392b;
}
.remove-btn:hover {
  background-color: #e74c3c;
}
</style>
