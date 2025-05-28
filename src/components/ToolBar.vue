<template>
  <header class="toolbar">
    <div class="left">
      <button @click="$emit('new-session')">🆕 新建</button>
      <button @click="$emit('save-session')">💾 儲存</button>
      <button @click="$emit('load-session')">📂 載入</button>
      <button @click="$emit('clear-workspace')">🗑 清空</button>
    </div>
    <div class="right">
      <button @click="$emit('export-prompt')">📤 匯出 Prompt</button>
    </div>
  </header>
</template>

<script>
export default {
  name: 'ToolBar'
};
</script>

<style scoped>
.toolbar {
  background-color: #444;
  color: white;
  padding: 0.5em 1em;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
button {
  margin-right: 0.5em;
  padding: 0.3em 0.8em;
  background-color: #666;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
button:hover {
  background-color: #888;
}
.right button {
  background-color: #357edd;
}
.right button:hover {
  background-color: #5599ee;
}
</style>
