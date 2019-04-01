<!-- 添加用户对话框 -->
<template>
  <div>
    <Modal
      title="添加已有用户"
      width="700"
      v-model="isShowModal"
      @on-ok="onClickOk"
      @on-cancel="onClickCancel"
    >
      <K2Transfer
        :data="user.data"
        filterable
        :style="{width: '702px', margin: '0 auto'}"
        :list-style="{height: '400px', width: '300px'}"
        :target-keys="user.selectKeys"
        :selected-keys="user.selectKeys"
        :titles="user.titles"
        @on-change="handleChange"
        @on-dblclick="handleChange">
      </K2Transfer>
    </Modal>
  </div>
</template>

<script>
import { Modal } from 'iview'
import K2Transfer from '@/components/kfc-k2transfer'

import { api } from '../api'

export default {
  name: 'UserEdit',
  components: {
    Modal,
    K2Transfer
  },
  props: {
    currentGroup: {
      type: Object,
      required: true
    },
    isShowUserModal: {
      type: Boolean,
      required: true
    }
  },
  data () {
    return {
      isShowModal: this.isShowUserModal,
      user: {
        titles: ['未选用户', '已选用户'],
        filterKey: 'name',
        filterLabel: 'name',
        data: [],
        selectKeys: []
      }
    }
  },
  watch: {
    isShowUserModal: {
      handler (curVal, oldVal) {
        this.isShowModal = curVal
        // 清空选中用户
        this.user.selectKeys.splice(0, this.user.selectKeys.length)
        if (curVal) {
          this.getUserList()
        }
      }
    }
  },
  methods: {
    // 添加用户
    onClickOk () {
      let users = []
      this.user.selectKeys.forEach(item => {
        users.push({
          name: item
        })
      })

      this.$axios.put(`${api.groups}/${this.currentGroup.id}/add`, { users: users }).then(res => {
        this.$emit('on-submit')
      })
    },
    onClickCancel () {
      this.$emit('on-close')
    },
    getUserList () {
      // 获取所有用户
      this.$axios.get(`${api.users}?type=all`).then(res => {
        this.user.data = res.data.result.map(item => {
          return {
            key: item.name,
            label: item.name
          }
        })
      })
      // 获取当前用户组已有用户列表
      this.$axios.get(`${api.users}?type=all&groupname=${this.currentGroup.name}`).then(res => {
        this.user.selectKeys = res.data.result.map(item => item.name)
      })
    },
    handleChange (selectedFields) {
      this.user.selectKeys = selectedFields
    }
  }
}
</script>

<style lang="css" scoped>
.form-card {
  height: 280px;
}
.form-panel {
  height: 280px;
  padding: 10px;
  border: 1px solid #dcdee2;
  border-radius: 3px;
  overflow: auto;
}
</style>
