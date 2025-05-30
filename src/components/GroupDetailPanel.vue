<template>
  <div class="detail-panel">
    <h3>Group 設定</h3>

    <div class="form-group">
      <label>群組名稱</label>
      <input type="text" v-model="localBlock.groupName" @input="emitUpdate" />
    </div>

    <div class="form-group">
      <label>群組類型</label>
      <p>{{ localBlock.groupType === 'merge' ? '拼裝群組' : '邏輯群組' }}</p>
    </div>

    <div class="form-group">
      <label>包含的模組</label>
      <ul>
        <li v-for="prompt in localBlock.children" :key="prompt">
          {{ prompt }}
          <button class="remove-btn" @click="removeChild(prompt)">❌</button>
        </li>
      </ul>
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
  watch: {
    block(newVal) {
      this.localBlock = JSON.parse(JSON.stringify(newVal));
    }
  },
  methods: {
    save() {
      this.$emit('save', this.localBlock);
    },
    removeChild(prompt) {
      this.localBlock.children = this.localBlock.children.filter(p => p !== prompt);
      this.emitUpdate();
      this.$emit('remove-child', prompt);
    },
    emitUpdate() {
      this.$emit('update', this.localBlock);
    }
  }
};
</script>
