<template>
  <component
    :is="block?.type === 'group' ? 'GroupDetailPanel' : 'PromptDetailPanel'"
    :block="block"
    :all-blocks="allBlocks"
    @update="onUpdate"
    @delete-block="onDeleteBlock"
    @remove-child="onRemoveChild"
  />
</template>

<script>
import PromptDetailPanel from './PromptDetailPanel.vue'
import GroupDetailPanel from './GroupDetailPanel.vue'

export default {
  name: 'DetailPanel',
  components: {
    PromptDetailPanel,
    GroupDetailPanel
  },
  props: ['block', 'allBlocks'],
  emits: ['update', 'delete-block', 'remove-child'],
  methods: {
    onUpdate(updatedBlock) {
      this.$emit('update', updatedBlock);
    },
    onDeleteBlock(target) {
      this.$emit('delete-block', target);
    },
    onRemoveChild(prompt) {
      this.$emit('remove-child', prompt);
    }
  }
}
</script>
