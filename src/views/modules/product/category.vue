<template>
  <div>
    <el-tree
      :data="data"
      :props="defaultProps"
      node-key="catId"
      show-checkbox
      :expand-on-click-node="false"
      :default-expanded-keys="expanded"
    >
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="node.level <= 2"
            type="text"
            size="mini"
            @click="() => append(data)"
          >
            Append
          </el-button>
          <el-button
            v-if="node.childNodes.length == 0"
            type="text"
            size="mini"
            @click="() => remove(node, data)"
          >
            Delete
          </el-button>
        </span>
      </span>
    </el-tree>
    <el-dialog title="添加分类" :visible.sync="dialogVisible" width="30%">
      <el-form :model="form">
        <el-form-item label="分类名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addCategory">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data () {
    return {
      data: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      },
      expanded: [],
      dialogVisible: false,
      form: {
        name: '',
        parentCid: 0,
        catLevel: 0,
        showStatus: 1,
        sort: 0
      },
      formLabelWidth: '75px'
    }
  },
  methods: {
    append (data) {
      this.dialogVisible = true
      this.form.parentCid = data.catId
      this.form.catLevel = data.catLevel + 1
    },

    addCategory () {
      this.dialogVisible = false
      this.$http({
        url: this.$http.adornUrl('/product/category/save'),
        method: 'post',
        data: this.$http.adornData(this.form, false)
      }).then(({ data }) => {
        this.$message({
          message: '添加成功',
          type: 'success'
        })
        this.expanded = [this.form.parentCid]
        this.getCategoryListTree()
      })
    },

    remove (node, data) {
      console.log(node)
      var ids = [data.catId]
      this.$confirm(`是否删除 [${data.name}] 菜单?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/product/category/delete'),
          method: 'post',
          data: this.$http.adornData(ids, false)
        }).then(({ data }) => {
          this.$message({
            message: '删除成功',
            type: 'success'
          })
          this.expanded = [node.parent.data.catId]
          this.getCategoryListTree()
        })
      }).catch(() => {
        this.$message.error({
          type: 'info',
          message: '已取消删除'
        })
      })
    },

    getCategoryListTree () {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({ data }) => {
        this.data = data.data
      })
    }
  },
  created () {
    this.getCategoryListTree()
  }
}
</script>

<style>
.custom-tree-node {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 14px;
  padding-right: 8px;
}
</style>
