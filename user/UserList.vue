<!-- 用户列表 -->
<template>
  <div style="height: 100%;">
    <Row :gutter="16" class="margin-bottom">
      <Col span="14">
      <Button
        type="primary"
        @click="isShowUserModal = true"
      >
        添加已有用户
      </Button>
      </Col>
      <Col span="10">
      <Input
        placeholder="搜索用户"
        v-model="user.filterName"
        @on-change="onSearchClick"></Input>
      </Col>
    </Row>
    <UserEdit
      :currentGroup="currentGroup"
      :isShowUserModal="isShowUserModal"
      v-if="currentGroup"
      @on-submit="reloadUserList"
      @on-close="isShowUserModal = false" />
    <Table
      :columns="user.columns"
      :data="user.data"
      size="small"
      :loading="user.loading"
      class="margin-bottom"></Table>
    <Page
      :total="user.total"
      class="page-container"
      @on-change="onPageChange"
    />
  </div>
</template>
<script>
// eslint-disable-next-line
import { Table, Page, Icon, Row, Col, Button, Input } from 'iview'
import UserEdit from './UserEdit.vue'

import { api } from '../api'

export default {
  name: 'UserList',
  components: {
    Row,
    Col,
    Button,
    Input,
    Table,
    Page,
    UserEdit
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
      isShowUserModal: false,
      user: {
        filterName: '',
        page: 1,
        size: 10,
        total: 0,
        loading: false,
        data: [],
        columns: [
          { title: '用户名', key: 'name', minWidth: 110 },
          { title: '邮箱', key: 'email', minWidth: 130 },
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
                      this.deleteUser(params.row.name)
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
          this.getUserList()
        }
      }
    }
  },
  mounted () {
    if (this.currentGroup) {
      this.getUserList()
    }
  },
  methods: {
    // 删除用户
    deleteUser (name) {
      this.$axios.put(`${api.groups}/${this.currentGroup.id}/remove`, { users: [{ name: name }] }).then(res => {
        this.getUserList()
      })
    },
    // 获取用户列表
    getUserList () {
      this.user.loading = true
      let { page, size } = this.user
      let name = this.currentGroup.name

      if (this.user.filterName) {
        // url + 过滤条件
      }

      this.$axios.get(`${api.users}/?page=${page}&size=${size}&groupname=${name}`).then(res => {
        this.user.data = res.data.result
        this.user.total = res.data.pages.total
        this.user.loading = false
      })
    },
    // 切换页数
    onPageChange (page) {
      this.user.page = page
      this.getUserList()
    },
    onSearchClick () {
      this.getUserList()
    },
    reloadUserList () {
      this.isShowUserModal = false
      if (this.currentGroup) {
        // 添加用户后刷新用户列表页面
        this.getUserList()
      }
    }
  }
}
</script>
