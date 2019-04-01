<!-- 添加角色对话框 -->
<template>
  <Modal
    title="添加已有角色"
    width="700"
    v-model="isShowModal"
    @on-ok="onClickOk"
    @on-cancel="onClickCancel"
  >
    <K2Transfer
      :data="getData"
      filterable
      :style="{width: '702px', margin: '0 auto'}"
      :list-style="{height: '400px', width: '300px'}"
      :target-keys="role.selectRole"
      :selected-keys="role.selectRole"
      :titles="role.titles"
      @on-change="handleChange"
      @on-dblclick="handleChange">
    </k2transfer>
  </Modal>
</template>

<script>
import { Modal } from 'iview'
import K2Transfer from '@/components/kfc-k2transfer'

import { api } from '../api'

export default {
  name: 'RoleEdit',
  components: {
    Modal,
    K2Transfer
  },
  props: {
    currentGroup: {
      type: Object,
      required: true
    },
    isShowRoleModal: {
      type: Boolean,
      required: true
    }
  },
  data () {
    return {
      listStyle: {
        width: '300px',
        height: '300px'
      },
      isShowModal: this.isShowRoleModal,
      role: {
        data: [],
        selectRole: [],
        titles: ['未选角色', '已选角色']
      }
    }
  },
  computed: {
    getData () {
      let data = this.role.data
      return data.map(item => {
        return {
          key: item.name,
          label: item.name
        }
      })
    }
  },
  watch: {
    isShowRoleModal: {
      handler (curVal, oldVal) {
        this.isShowModal = curVal
        // 清空选中参数
        this.role.selectRole.splice(0, this.role.selectRole.length)
        if (curVal) {
          this.getSelectRole()
        }
      }
    }
  },
  mounted () {
    this.getAllRole()
  },
  methods: {
    // 添加角色
    onClickOk () {
      let roles = []
      this.role.selectRole.forEach(item => {
        roles.push({
          name: item
        })
      })

      this.$axios.put(`${api.groups}/${this.currentGroup.id}/add`, { roles: roles }).then(res => {
        this.$emit('on-submit')
      })
    },
    onClickCancel () {
      this.$emit('on-close')
    },
    getAllRole () {
      this.$axios.get(`${api.roles}?type=all`).then(res => {
        this.role.data = res.data.result
      })
    },
    getSelectRole () {
      let groupId = this.currentGroup.id
      this.$axios.get(`${api.roles}?type=all&groupId=${groupId}`).then(res => {
        this.role.selectRole = res.data.result.map(item => item.name)
      })
    },
    handleChange (selection) {
      this.role.selectRole = selection
    }
  }
}
</script>
