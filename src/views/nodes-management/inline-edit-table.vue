<template>
  <div class="app-container">
    <el-table v-loading="listLoading" :data="list" border fit highlight-current-row style="width: 100%">
      <el-table-column align="center" label="ID" width="80">
        <template slot-scope="{row}">
          <span>{{ row.id }}</span>
        </template>
      </el-table-column>

      <el-table-column min-width="300px" label="IP地址">
        <template slot-scope="{row}">
          <template v-if="row.edit">
            <el-input v-model="row.ip" class="edit-input" size="small" />
          </template>
          <span v-else>{{ row.ip }}</span>
        </template>
      </el-table-column>

      <el-table-column min-width="300px" label="端口">
        <template slot-scope="{row}">
          <template v-if="row.edit">
            <el-input v-model="row.port" class="edit-input" size="small" />
          </template>
          <span v-else>{{ row.port }}</span>
        </template>
      </el-table-column>

      <el-table-column align="center" label="操作" min-width="180px">
        <template slot-scope="{row}">
          <template v-if="row.edit">
            <el-button
              class="confirm-btn"
              type="success"
              size="small"
              icon="el-icon-circle-check-outline"
              @click="confirmEdit(row)"
            >
              Ok
            </el-button>

            <el-button
              class="cancel-btn"
              size="small"
              icon="el-icon-refresh"
              type="warning"
              @click="cancelEdit(row)"
            >
              cancel
            </el-button>
          </template>
          <template v-else>
            <el-button
              type="primary"
              size="small"
              icon="el-icon-edit"
              @click="row.edit=!row.edit"
            >
              编辑
            </el-button>
          </template>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>

export default {
  name: 'InlineEditTable',
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'info',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      list: null,
      listLoading: true,
      listQuery: {
        page: 1,
        limit: 10
      }
    }
  },
  created() {
    this.getList()
  },
  methods: {
    async getList() {
      this.listLoading = true
      // const { data } = await fetchList(this.listQuery)
      // const items = data.items
      const sensors = window.eel.get_sensors()
      /* const items = [
        {
          'id': 1,
          'ip': '192.168.12.114',
          'port': '2001',
          'edit': false
        }
      ] */
      this.list = sensors.map(v => {
        this.$set(v, 'edit', false) // https://vuejs.org/v2/guide/reactivity.html
        v.originalIp = v.ip //  will be used when user click the cancel botton
        v.originalPort = v.port //  will be used when user click the cancel botton
        return v
      })
      this.listLoading = false
    },
    cancelEdit(row) {
      row.ip = row.originalIp
      row.port = row.originalPort
      row.edit = false
      this.$message({
        message: '传感器取消修改，保持原设置',
        type: 'warning'
      })
    },
    confirmEdit(row) {
      row.edit = false
      row.originalTitle = row.title
      this.$message({
        message: '传感器设置修改成功',
        type: 'success'
      })
    }
  }
}
</script>

<style scoped>
.edit-input {
  padding-right: 100px;
}
.cancel-btn .confirm-btn {
  position: relative;
  right: 5px;
  top: 10px;
}
</style>
