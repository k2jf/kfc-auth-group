<!-- 角色列表 -->
<template>
  <div style="height: 100%;">
    <Row :gutter="16" class="margin-bottom">
      <Col span="14">
      <Button
        type="primary"
        @click="isShowRoleModal = true"
      >
        添加已有角色
      </Button>
      </Col>
      <Col span="10">
      <Input
        placeholder="搜索角色"
        v-model="role.filterName"
        @on-change="onSearchClick"></Input>
      </Col>
    </Row>
    <RoleEdit
      :currentGroup="currentGroup"
      :isShowRoleModal="isShowRoleModal"
      v-if="currentGroup"
      @on-submit="reloadRoleList"
      @on-close="isShowRoleModal = false" />
    <Table
      :columns="role.columns"
      :data="role.data"
      size="small"
      :loading="role.loading"
      class="margin-bottom"></Table>
  </div>
</template>
<script>
// eslint-disable-next-line
import { Table, Icon, Col, Row, Button, Input } from 'iview'
import RoleEdit from './RoleEdit.vue'

import { api } from '../api'

export default {
  name: 'RoleList',
  components: {
    Row,
    Col,
    Button,
    Input,
    Table,
    RoleEdit
  },
  props: {
    currentGroup: {
      type: Object,
      required: false,
      default: () => {
        return {}
      }
    }
  },
  data () {
    return {
      isShowRoleModal: false,
      role: {
        filterName: '',
        loading: false,
        page: 1,
        size: 10,
        data: [],
        columns: [
          { title: '角色', key: 'name', minWidth: 110 },
          { title: '描述', key: 'description', minWidth: 130 },
          {
            title: '操作',
            width: 100,
            render: (h, params) => {
              return h(
                'span',
                {
                  style: {
                    cursor: 'pointer'
                  },
                  on: {
                    click: () => {
                      this.deleteRole(params.row.name)
                    }
                  }
                },
                [
                  h(
                    'Icon',
                    {
                      props: {
                        type: 'ios-trash',
                        size: 16,
                        color: '#5cadff'
                      }
                    }
                  ),
                  h(
                    'span', '移除'
                  )
                ]
              )
            }
          }
        ]
      }
    }
  },
  watch: {
    currentGroup: {
      handler (curVal, oldVal) {
        if (curVal && curVal.id) {
          this.getRoleList()
        }
      }
    }
  },
  mounted () {
    if (this.currentGroup) {
      this.getRoleList()
    }
  },
  methods: {
    // 删除角色
    deleteRole (name) {
      this.$axios.put(`${api.groups}/${this.currentGroup.id}/remove`, { roles: [{ name: name }] }).then(res => {
        this.getRoleList()
      })
    },
    // 获取角色列表
    getRoleList () {
      this.role.loading = true
      let { page, size } = this.role
      let groupId = this.currentGroup.id

      if (this.role.filterName) {
        // url + 过滤条件
      }

      this.$axios.get(`${api.roles}?page=${page}&size=${size}&groupId=${groupId}`).then(res => {
        this.role.data = res.data.result
        this.role.total = res.data.pages.total
        this.role.loading = false
      })
    },
    onSearchClick () {
      this.getRoleList()
    },
    reloadRoleList () {
      this.isShowRoleModal = false
      if (this.currentGroup) {
        // 添加角色后刷新角色列表页面
        this.getRoleList()
      }
    }
  }
}
</script>
