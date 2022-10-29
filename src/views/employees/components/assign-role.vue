<template>
  <el-dialog title="分配角色" :visible="showRoleDialog" @close="btnCancel">
    <!-- 多选框组件需要 v-model双向绑定 -->
    <el-checkbox-group v-model="roleIds">
      <!-- 放置当前所有的角色内容 -->
      <!-- 显示角色名称 存储角色id -->
      <el-checkbox v-for="item in list" :key="item.id" :label="item.id">{{ item.name }}</el-checkbox>
    </el-checkbox-group>
    <template #footer>
      <el-row type="flex" justify="center">
        <el-col :span="6">
          <el-button size="small" type="primary" @click="btnOK">确定</el-button>
          <el-button size="small" @click="btnCancel">取消</el-button>
        </el-col>
      </el-row>
    </template>
  </el-dialog>
</template>

<script>
import { getRoleList } from '@/api/setting'
import { assignRoles } from '@/api/employees'
import { getUserDetailById } from '@/api/user'
export default {
  props: {
    showRoleDialog: {
      type: Boolean,
      default: false
    },
    userId: {
      type: String,
      default: null
    }
  },
  data() {
    return {
      list: [], // 负责存储当前所有角色的id
      roleIds: [] // 这个数组负责存储 当前用户所拥有的角色id
    }
  },
  created() {
    this.getRoleList()
  },
  methods: {
    async getRoleList() {
      // 这里只查前十条 因为目前没有相应的获取 所有数据的接口
      // total 1
      const { rows } = await getRoleList()
      this.list = rows
    },
    // props的传值时异步的，此方法父组件调用
    async  getUserDetailById(id) {
      const { roleIds } = await getUserDetailById(id)
      this.roleIds = roleIds // 将当前的用户角色赋值给选中对象
    },
    // 分配角色
    async btnOK() {
      await assignRoles({ id: this.userId, roleIds: this.roleIds }) // 保存用户的角色
      this.$emit('update:showRoleDialog', false)
    },
    btnCancel() {
      this.roleIds = []
      this.$emit('update:showRoleDialog', false)
      // this.$parent.showRoleDialog = false //两种方法都可以关闭弹层
    }
  }
}
</script>

<style>

</style>
