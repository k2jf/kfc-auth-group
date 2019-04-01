<template>
  <Card class="container">
    <Split v-model="split">
      <div slot="left">
        <GroupInfo @on-group-change="getCurrentGroup" />
      </div>
      <div slot="right">
        <Tabs
          type="line"
          v-model="currentTab">
          <div slot="extra">
            <Button
              type="primary"
              style="margin: 20px"
              v-if="currentTab === 'role'"
              @click="isShowRoleModal = true"
            >
              添加已有角色
            </Button>
            <Button
              type="primary"
              style="margin: 20px"
              v-if="currentTab === 'user'"
              @click="isShowUserModal = true"
            >
              添加已有用户
            </Button>
          </div>
          <TabPane
            label="用户"
            name="user"
            class="tab-pane">
            <UserList
              :currentGroup="currentGroup"
              :isReloadUserList="isReloadUserList"
              v-if="currentGroup" />
            <UserEdit
              :currentGroup="currentGroup"
              :isShowUserModal="isShowUserModal"
              v-if="currentGroup"
              @on-submit="reloadUserList"
              @on-close="isShowUserModal = false" />
          </TabPane>
          <TabPane
            label="角色"
            name="role"
            class="tab-pane">
            <RoleList
              :currentGroup="currentGroup"
              :isReloadRoleList="isReloadRoleList"
              v-if="currentGroup" />
            <RoleEdit
              :currentGroup="currentGroup"
              :isShowRoleModal="isShowRoleModal"
              v-if="currentGroup"
              @on-submit="reloadRoleList"
              @on-close="isShowRoleModal = false" />
          </TabPane>
        </Tabs>
      </div>
    </Split>
  </Card>
</template>

<script>
import { Split, Button, Tabs, TabPane, Card } from 'iview'

import GroupInfo from './group'
import RoleList from './role'
import UserList from './user'
import RoleEdit from './role/RoleEdit.vue'
import UserEdit from './user/UserEdit.vue'

export default {
  name: 'UserGroups',
  components: {
    Split,
    Button,
    Tabs,
    TabPane,
    Card,
    GroupInfo,
    RoleList,
    RoleEdit,
    UserList,
    UserEdit
  },
  data () {
    return {
      split: 0.2,
      currentTab: 'user',
      isShowRoleModal: false,
      isShowUserModal: false,
      isReloadUserList: false,
      isReloadRoleList: false,
      currentGroup: null,
      permission: null
    }
  },
  methods: {
    getCurrentGroup (currentGroup) {
      this.currentGroup = currentGroup
    },
    reloadRoleList () {
      this.isShowRoleModal = false
      this.isReloadRoleList = !this.isReloadRoleList
    },
    reloadUserList () {
      this.isShowUserModal = false
      this.isReloadUserList = !this.isReloadUserList
    }
  }
}
</script>

<style lang="css" scoped>
.ivu-tabs {
  height: 100%;
}
</style>
