<template>
  <div>
    <div style="margin-top: 20px">
      <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                clearable
                @clear="initEmpPoints"
                style="width: 350px;margin-right: 10px" v-model="name"
                @keydown.enter.native="initEmpPoints"></el-input>
      <el-button icon="el-icon-search" type="primary" @click="initEmpPoints">
        搜索
      </el-button>
    </div>

    <div style="margin-top: 10px">
      <el-table
          :data="empPoints"
          border
          v-loading="loading"
          element-loading-text="正在加载..."
          element-loading-spinner="el-icon-loading"
          element-loading-background="rgba(0, 0, 0, 0.8)"
          size="small"
          style="width: 80%">
        <el-table-column
            type="selection"
            width="55">
        </el-table-column>
        <el-table-column
            prop="id"
            label="编号"
            width="55">
        </el-table-column>
        <el-table-column
            prop="workID"
            label="工号"
            width="120">
        </el-table-column>
        <el-table-column
            prop="name"
            label="姓名"
            width="120">
        </el-table-column>
        <el-table-column
            label="积分"
            width="150">
          <template slot-scope="scope">
            <el-tag type="success" v-if="scope.row.point>0">{{scope.row.point}}</el-tag>
            <el-tag type="danger" v-else>{{ scope.row.point }}</el-tag>
          </template>
        </el-table-column>
      </el-table>
      <div style="display: flex;justify-content: flex-start;margin-top: 10px">
        <el-pagination
            background
            @current-change="currentChange"
            @size-change="sizeChange"
            layout="sizes, prev, pager, next, jumper, ->, total, slot"
            :total="total">
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "StaPoint",
  data() {
    return {
      name: '',
      empPoints: [],
      loading: false,
      page: 1,
      size: 10,
      total: 0,
    }
  },
  mounted() {
    this.initEmpPoints();
  },
  methods: {
    sizeChange(currentSize) {
      this.size = currentSize;
      this.initEmpPoints();
    },
    currentChange(currentPage) {
      this.page = currentPage;
      this.initEmpPoints();
    },
    initEmpPoints() {
      this.loading = true;
      let url = '/statistics/point/?page='+ this.page + '&size=' + this.size;;
      if (this.name) {
        url += "&name=" + this.name;
      }
      this.getRequest(url).then(resp => {
        this.loading = false;
        if (resp) {
          this.empPoints = resp.list;
          this.total=resp.total;
        }
      })
    }
  }
}
</script>

<style scoped>

</style>