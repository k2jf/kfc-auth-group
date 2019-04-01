<!-- 用户列表 -->
<template>
  <div style="height: 100%;">
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
import { Table, Page, Icon } from 'iview'

import { api } from '../api'

export default {
  name: 'UserList',
  components: {
    Table,
    Page
  },
  props: {
    currentGroup: {
      type: Object,
      required: false,
      default: () => {
        return {}
      }
    },
    isReloadUserList: {
      type: Boolean,
      required: false
    }
  },
  data () {
    return {
      user: {
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
    },
    isReloadUserList: {
      handler (curVal, oldVal) {
        if (this.currentGroup) {
          // 添加用户后刷新用户列表页面
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
    }
  }
}
</script>
