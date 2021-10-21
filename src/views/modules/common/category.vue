<template>
  <el-tree
    :data="data"
    :props="defaultProps"
    node-key="catId"
    @node-click="nodeClick"
  >
  </el-tree>
</template>

<script>
export default {
  data () {
    return {
      data: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  methods: {
    getCategoryListTree () {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({ data }) => {
        this.data = data.data
      })
    },
    nodeClick (data, node) {
      this.$emit('category-node-click', data, node)
    }
  },
  created () {
    this.getCategoryListTree()
  }
}
</script>

<style>
</style>
