<template>
  <aside class="library">
    <h3>模組庫</h3>
    <div class="block-list">
      <select v-if="types.length" v-model="selectedType">
        <option value="">全部</option>
        <option v-for="type in types" :key="type">{{ type }}</option>
      </select>

      <div
        class="block-item"
        v-for="(block, i) in filteredModules"
        :key="block.id"
        @click="$emit('add-block', cloneBlock(block))"
      >
        {{ block.prompt }}
      </div>

      <hr />
      <button @click="addEmptyGroup('merge')">➕ Merge Group</button>
      <button @click="addEmptyGroup('logic')">➕ Logic Group</button>
    </div>
  </aside>
</template>

<script>
import { nanoid } from 'nanoid';

export default {
  name: 'ModuleLibrary',
  data() {
    return {
      modules: [],
      selectedType: '',
      groupIdCounter: 0
    }
  },
  computed: {
    types() {
      return [...new Set(this.modules.map(m => m.type))];
    },
    filteredModules() {
      return this.selectedType
        ? this.modules.filter(m => m.type === this.selectedType)
        : this.modules;
    }
  },
  methods: {
    async fetchModule(file) {
      const res = await fetch(`/data/modules/${file}`);
      if (!res.ok) {
        console.error(`無法載入模組檔案: ${file}`);
        return [];
      }
      const json = await res.json();
      return json.items.map(entry => ({
        id: nanoid(),
        prompt: entry.prompt,
        type: json.type,
        weight: 1.0,
        x: 100,
        y: 100
      }));
    },
    async loadAllModules() {
      try {
        const indexRes = await fetch('/data/modules/modules.json');
        const moduleList = await indexRes.json();
        const promises = moduleList.map(file => this.fetchModule(file));
        const results = await Promise.all(promises);
        this.modules = results.flat();
      } catch (err) {
        console.error('載入模組索引失敗', err);
      }
    },
    cloneBlock(block) {
      return {
        ...block,
        id: nanoid(),
        x: 100,
        y: 100
      };
    },
    addEmptyGroup(groupType) {
      this.$emit('add-block', {
        id: `group-${nanoid()}`,
        type: 'group',
        groupType,
        groupName: `${groupType.toUpperCase()} GROUP`,
        children: [],
        x: 100,
        y: 100,
        width: 180,
        height: 120
      });
    }
  },
  mounted() {
    this.loadAllModules();
  }
}
</script>

<style scoped>
.library {
  width: 200px;
  background-color: var(--lib-bg);
  color: var(--text-color);
  padding: 1em;
  height: 100%;
  overflow-y: auto;
}
.block-list {
  display: flex;
  flex-direction: column;
  gap: 0.5em;
}
.block-item {
  background-color: var(--bg-block);
  border: 1px solid var(--block-border);
  color: var(--text-color);
  padding: 0.5em;
  cursor: pointer;
  border-radius: 4px;
  text-align: center;
  font-weight: 500;
}
select {
  width: 100%;
  margin-bottom: 1em;
  padding: 0.3em;
  font-size: 1em;
  border-radius: 4px;
  border: 1px solid var(--block-border);
  background-color: var(--bg-block);
  color: var(--text-color);
}
button {
  background-color: var(--bg-block);
  border: 1px solid var(--block-border);
  padding: 0.3em;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9em;
  color: var(--text-color);
}
button:hover {
  background-color: var(--hover-bg);
}
</style>
