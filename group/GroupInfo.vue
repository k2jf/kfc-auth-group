<!-- 用户组信息 -->
<template>
  <div>
    <Button
      type="default"
      long
      class="margin-bottom add-btn"
      @click="isShowGroupModal = true">
      创建用户组
    </Button>
    <Input
      placeholder="搜索用户组"
      class="margin-bottom"
      v-model="groupName"
      @on-change="onSearchClick" />
    <GroupEdit
      :isShowGroupModal="isShowGroupModal"
      class="margin-bottom"
      @on-submit="onReloadList"
      @on-close="isShowGroupModal = false" />
    <GroupList
      :groupData="groupData"
      :currentGroup="currentGroup"
      @on-select="onChangeGroup"
      @on-delete="onDeleteGroup" />
  </div>
</template>

<script>
import { Input, Button } from 'iview'
import GroupEdit from './GroupEdit.vue'
import GroupList from './GroupList.vue'

import { api } from '../api'

export default {
  name: 'GroupInfo',
  components: {
    Button,
    Input,
    GroupEdit,
    GroupList
  },
  data () {
    return {
      isShowGroupModal: false,
      groupName: '',
      groupData: null,
      currentGroup: null
    }
  },
  mounted () {
    this.getGroupList()
  },
  methods: {
    // 获取用户组列表
    getGroupList () {
      let url = `${api.groups}?type=all`
      if (this.groupName) {
        url += `&groupname=${this.groupName}`
      }

      this.$axios.get(url).then(res => {
        this.groupData = res.data.result
        if (this.currentGroup) return
        this.currentGroup = this.groupData[0]
        this.$emit('on-group-change', this.currentGroup)
      })
    },
    // 新建成功更新用户组列表
    onReloadList () {
      this.isShowGroupModal = false
      setTimeout(this.getGroupList, 5000)
    },
    // 选择用户组
    onChangeGroup (id) {
      this.currentGroup = this.groupData.find(item => item.id === id)
      this.$emit('on-group-change', this.currentGroup)
    },
    // 删除用户组
    onDeleteGroup (id) {
      this.$axios.delete(`${api.groups}/${id}`).then(res => {
        // 删除的用户组为当前选中用户组当前选中用户组设置为null
        if (this.currentGroup.id === id) {
          this.currentGroup = null
        }
        this.getGroupList()
      })
    },
    onSearchClick () {
      this.getGroupList()
    }
  }
}
</script>

<style lang="css" scoped>
.margin-bottom {
  margin-bottom: 6px;
}
</style>
