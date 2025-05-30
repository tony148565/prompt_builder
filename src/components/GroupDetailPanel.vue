<template>
  <div class="detail-panel">
    <h3>Group 設定</h3>

    <div class="form-group">
      <label>群組名稱</label>
      <input
        type="text"
        v-model="localBlock.groupName"
        :readonly="localBlock.groupType === 'merge'"
        @input="emitUpdate"
      />
    </div>

    <div class="form-group">
      <label>群組類型</label>
      <p>{{ localBlock.groupType === 'merge' ? '拼裝群組' : '邏輯群組' }}</p>
    </div>

    <div class="form-group">
      <label>包含的模組</label>
      <ul>
        <li v-for="(child, index) in localBlock.children" :key="child.prompt">
          <input
            type="text"
            v-model="child.prompt"
            @input="onChildEdit"
          />
          <button @click="moveUp(index)" :disabled="index === 0">⬆️</button>
          <button @click="moveDown(index)" :disabled="index === localBlock.children.length - 1">⬇️</button>
          <button class="remove-btn" @click="removeChild(child)">❌</button>
        </li>
      </ul>
    </div>

    <!-- 合併預覽 -->
    <div v-if="localBlock.groupType === 'merge'" class="form-group">
      <label>合併預覽</label>
      <p style="font-weight: bold">{{ localBlock.groupName }}</p>
    </div>

    <button @click="save">儲存</button>
  </div>
</template>

<script>
export default {
  name: 'GroupDetailPanel',
  props: ['block', 'allBlocks'],
  emits: ['save', 'update', 'remove-child'],
  data() {
    return {
      localBlock: JSON.parse(JSON.stringify(this.block))
    };
  },
  methods: {
    save() {
      this.$emit('save', this.localBlock);
    },
    removeChild(child) {
      this.localBlock.children = this.localBlock.children.filter(c => c.prompt !== child.prompt);
      this.updateMergeGroupName(); // 👈 加這行，保證名稱同步
      this.emitUpdate();
      this.$emit('remove-child', child.prompt);
    },
    emitUpdate() {
      this.$emit('update', this.localBlock);
    },
    updateMergeGroupName() {
      if (this.localBlock.groupType === 'merge') {
        this.localBlock.groupName = this.localBlock.children.map(c => c.prompt).join(' ');
      }
    },
    moveUp(index) {
      if (index > 0) {
        const tmp = this.localBlock.children[index];
        this.localBlock.children.splice(index, 1);
        this.localBlock.children.splice(index - 1, 0, tmp);
        this.updateMergeGroupName();
        this.emitUpdate();
      }
    },
    moveDown(index) {
      if (index < this.localBlock.children.length - 1) {
        const tmp = this.localBlock.children[index];
        this.localBlock.children.splice(index, 1);
        this.localBlock.children.splice(index + 1, 0, tmp);
        this.updateMergeGroupName();
        this.emitUpdate();
      }
    },
    onChildEdit() {
      this.updateMergeGroupName();
      this.emitUpdate();
    }
  },
  watch: {
    block(newBlock) {
      this.localBlock = JSON.parse(JSON.stringify(newBlock));
      this.updateMergeGroupName(); // 👈 保證初始化也更新
    }
  }
};
</script>

<style scoped>
.detail-panel {
  height: 100%;
  width: 250px;
  background-color: var(--panel-bg);
  padding: 1em;
  border-left: 1px solid var(--block-border);
}
.form-group {
  margin-bottom: 1em;
}
input[type="text"] {
  width: 100%;
  padding: 0.4em;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1em;
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
  color: white;
}
.remove-btn:hover {
  background-color: #e74c3c;
}
</style>
