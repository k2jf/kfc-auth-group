<!-- 用户组列表信息 -->
<template>
  <CellGroup class="left-list-pane" @on-click="onSelectClick">
    <Cell
      :selected="currentGroup && groupItem.id === currentGroup.id"
      :name="groupItem.id"
      v-for="groupItem in groupData"
      :key="groupItem.id">
      <Icon
        type="ios-people"
        size="18"
        color="#5cadff"
        slot="icon" />
      <span
        class="c-pointer"
        slot="label"
      >{{ groupItem.name }}</span>
      <span slot="extra" @click="onDeleteClick(groupItem.id)"><Icon
        type="ios-trash"
        size="16"
        color="#5cadff" /></span>
    </Cell>
  </CellGroup>
</template>

<script>
import { Icon, CellGroup, Cell } from 'iview'

export default {
  name: 'GroupList',
  components: {
    Icon,
    CellGroup,
    Cell
  },
  props: {
    groupData: {
      type: Array,
      required: false,
      default: () => {
        return []
      }
    },
    currentGroup: {
      type: Object,
      required: false,
      default: () => {
        return {}
      }
    }
  },
  methods: {
    onDeleteClick (id) {
      this.$emit('on-delete', id)
    },
    onSelectClick (id) {
      this.$emit('on-select', id)
    }
  }
}
</script>
