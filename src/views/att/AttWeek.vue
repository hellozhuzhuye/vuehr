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
      <el-button  type="primary" @click="initAttList" >
        << 上一周
      </el-button>
      <el-tag size="big" style="margin-left: 10px;margin-right: 10px">
        {{currentWeek}}
      </el-tag>

      <el-button  type="primary" @click="initAttList" >
        下一周 >>
      </el-button>

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
            prop="normal"
            width="100"
            align="left"
            label="正常">
        </el-table-column>
        <el-table-column
            prop="late"
            width="100"
            align="left"
            label="迟到">
        </el-table-column>
        <el-table-column
            prop="early"
            width="100"
            align="left"
            label="早退">
        </el-table-column>
        <el-table-column
            prop="asked"
            width="100"
            align="left"
            label="请假">
        </el-table-column>
        <el-table-column
            prop="workout"
            width="100"
            align="left"
            label="出差">
        </el-table-column>
        <el-table-column
            prop="leaved"
            width="100"
            align="left"
            label="旷工">
        </el-table-column>
        <el-table-column
            prop="remark"
            width="300"
            align="left"
            label="备注">
        </el-table-column>
        <el-table-column
            align="left"
            width="300"
            label="操作">
          <template slot-scope="scope">
            <el-button @click="showEditEmpView(scope.row)" style="padding: 3px" size="mini">编辑</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="display: flex;justify-content: flex-end;margin-top: 10px">
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
  name: "AttWeek",
  data(){
    return {
      currentWeek:"",
      attData:[],
      ename: "",
      page: 1,
      size: 10,
      total: 0
    }
  },
  methods:{
    initCurrentWeek(){
      this.getRequest("/attendance/empatt/currentWeek").then(resp => {
        if (resp) {
          this.currentWeek = resp.obj;
        }
      })
    },
    sizeChange(currentSize) {
      this.size = currentSize;
      this.initAttList();
    },
    currentChange(currentPage) {
      this.page = currentPage;
      this.initAttList();
    },
    initAttList(){
      let url='/attendance/empatt/week?page=' + this.page + '&size=' + this.size;
      if (this.ename){
        url += "&name=" + this.ename;
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
    this.initCurrentWeek();
  }
}
</script>

<style scoped>

</style>