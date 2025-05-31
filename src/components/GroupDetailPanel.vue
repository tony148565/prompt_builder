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

    <!-- 分派子元件 -->
    <MergeGroupEditor
      v-if="localBlock.groupType === 'merge'"
      v-model="localBlock.children"
      @update="emitUpdate"
    />
    <LogicGroupEditor
      v-else
      v-model="localBlock.children"
      :all-blocks="allBlocks"
      @update="emitUpdate"
    />

    <button @click="save">儲存</button>
  </div>
</template>

<script>
import MergeGroupEditor from './MergeGroupEditor.vue';
import LogicGroupEditor from './LogicGroupEditor.vue';

export default {
  name: 'GroupDetailPanel',
  props: ['block', 'allBlocks'],
  components: { MergeGroupEditor, LogicGroupEditor },
  emits: ['save', 'update'],
  data() {
    return {
      localBlock: JSON.parse(JSON.stringify(this.block))
    };
  },
  methods: {
    save() {
      this.$emit('save', this.localBlock);
    },
    emitUpdate() {
      this.$emit('update', this.localBlock);
    }
  },
  watch: {
    block(newBlock) {
      this.localBlock = JSON.parse(JSON.stringify(newBlock));
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
  margin-top: 0.5em;
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
</style>
