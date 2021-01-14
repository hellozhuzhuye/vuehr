<template>
<div>

  <div>
    <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
              clearable
              @clear="initAttList"
              style="width: 350px;margin-right: 10px" v-model="ename"
              @keydown.enter.native="initAttList" ></el-input>
    <el-button icon="el-icon-search" type="primary" @click="initAttList" >
      搜索
    </el-button>
    <el-date-picker style="margin-left: 20px"
        v-model="beginDateScope"
        type="daterange"
        size="mini"
        unlink-panels
        value-format="yyyy-MM-dd"
        range-separator="至"
        start-placeholder="开始日期"
        end-placeholder="结束日期">
    </el-date-picker>
  </div>
  <div style="margin-top: 10px">
    <el-table
        :data="attData"
        stripe
        border
        element-loading-text="正在加载..."
        element-loading-spinner="el-icon-loading"
        element-loading-background="rgba(0, 0, 0, 0.8)"
        style="width: 100%">
      <el-table-column
          type="selection"
          width="60">
      </el-table-column>
      <el-table-column
          prop="workID"
          label="工号"
          align="left"
          width="100">
      </el-table-column>
      <el-table-column
          prop="dname"
          width="120"
          align="left"
          label="所属部门">
      </el-table-column>
      <el-table-column
          prop="ename"
          fixed
          align="left"
          label="姓名"
          width="100">
      </el-table-column>
      <el-table-column
          prop="date"
          width="120"
          align="left"
          label="日期">
      </el-table-column>
      <el-table-column
          prop="weekOfDay"
          width="120"
          align="left"
          label="星期">
      </el-table-column>
      <el-table-column
          prop="morning"
          width="220"
          align="left"
          label="上午打卡时间">
      </el-table-column>
      <el-table-column
          width="100"
          align="left"
          label="状态">
      <template slot-scope="scope">
        <el-tag  type="success" v-if="scope.row.morningStatus==0">正常</el-tag>
        <el-tag  type="danger" v-else-if="scope.row.morningStatus==1">迟到</el-tag>
        <el-tag  type="danger" v-else-if="scope.row.morningStatus==2">早退</el-tag>
        <el-tag  v-else-if="scope.row.morningStatus==4">请假</el-tag>
        <el-tag  v-else-if="scope.row.morningStatus==3">出差</el-tag>
        <el-tag type="danger" v-else >旷工</el-tag>
      </template>
      </el-table-column>
      <el-table-column
          prop="afternoon"
          width="220"
          align="left"
          label="下午打卡时间">
      </el-table-column>
      <el-table-column
          width="100"
          align="left"
          label="状态">
        <template slot-scope="scope">
          <el-tag  type="success" v-if="scope.row.afternoonStatus==0">正常</el-tag>
          <el-tag  type="danger" v-else-if="scope.row.afternoonStatus==1">迟到</el-tag>
          <el-tag  type="danger" v-else-if="scope.row.afternoonStatus==2">早退</el-tag>
          <el-tag   v-else-if="scope.row.afternoonStatus==4">请假</el-tag>
          <el-tag   v-else-if="scope.row.afternoonStatus==3">出差</el-tag>
          <el-tag type="danger" v-else >旷工</el-tag>
        </template>
      </el-table-column>
      <el-table-column
          prop="remark"
          width="200"
          align="left"
          label="备注">
      </el-table-column>
      <el-table-column
          fixed="right"
          width="200"
          label="操作">
        <template slot-scope="scope">
          <el-button @click="showEditEmpView(scope.row)" style="padding: 3px" size="mini">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div style="display: flex;justify-content: flex-end">
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
  name: "AttList",
  data(){
    return {
      attData:[],
      ename: "",
      page: 1,
      size: 10,
      total: 0,
      beginDateScope: null
    }
  },
  methods:{
    sizeChange(currentSize) {
      this.size = currentSize;
      this.initAttList();
    },
    currentChange(currentPage) {
      this.page = currentPage;
      this.initAttList();
    },
    initAttList(){
      let url='/attendance/empatt/?' + this.page + '&size=' + this.size;
      if (this.ename){
        url += "&name=" + this.ename;
      }
      if (this.beginDateScope) {
        url += '&beginDateScope=' + this.beginDateScope;
      }
      this.getRequest(url).then(resp => {
        if (resp) {
          this.attData = resp.obj.list;
          this.total = resp.obj.total;
        }
      })
    }
  },
  mounted(){
    this.initAttList();
  }
}
</script>

<style scoped>

</style>