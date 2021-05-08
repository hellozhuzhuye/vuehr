<template>
  <div>
    <div style="margin-top: 10px">
      <el-table
          :data="myApplys"
          border
          v-loading="loading"
          element-loading-text="正在加载..."
          element-loading-spinner="el-icon-loading"
          element-loading-background="rgba(0, 0, 0, 0.8)"
          size="small"
          @selection-change="handleSelectionChange"
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
            prop="applyTypeName"
            label="申请类型"
            width="100">
        </el-table-column>
        <el-table-column
            prop="content"
            label="申请内容"
            width="200">
        </el-table-column>
        <el-table-column
            prop="selectDateTime"
            label="开始时间与结束时间"
            width="250">
        </el-table-column>
        <el-table-column
            prop="hrNames"
            label="审核人"
            width="200">
        </el-table-column>
        <el-table-column
            prop="createTime"
            label="申请时间"
            width="200">
        </el-table-column>
        <el-table-column
            label="操作">
          <template slot-scope="scope">
            <el-button type="primary" size="small" @click="showDetailView(scope.row)">详细</el-button>
<!--            <el-button size="small" type="danger" @click="deleteHandler(scope.row)">删除</el-button>-->
          </template>
        </el-table-column>
      </el-table>


    </div>

    <el-dialog
        title="申请详情"
        :visible.sync="dialogVisible"
        width="60%">

      <el-table
          :data="detailApply"
          border
          v-loading="loading"
          element-loading-text="正在加载..."
          element-loading-spinner="el-icon-loading"
          element-loading-background="rgba(0, 0, 0, 0.8)"
          size="small"
          @selection-change="handleSelectionChange"
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
            prop="hrName"
            label="审核人"
            width="100">
        </el-table-column>
        <el-table-column
            label="审核状态"
            width="200">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status===0">未审核</el-tag>
            <el-tag type="success" v-else-if="scope.row.status===1">批准</el-tag>
            <el-tag type="danger" v-else-if="scope.row.status===2">未批准</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="content"
            label="附注"
            width="200">
        </el-table-column>
        <el-table-column
            prop="approveTime"
            label="审核时间"
            width="200">
        </el-table-column>
      </el-table>

      <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="updateCancel">取 消</el-button>
    <el-button size="small" type="primary" @click="doUpdate">确 定</el-button>
  </span>
    </el-dialog>


  </div>
</template>

<script>
export default {
  name: "WorkFlowMy",
  data() {
    return {
      myApplys:[],
      dialogVisible: false,
      loading: false,
      multipleSelection: [],
      wfid:0,
      detailApply:[],
      ecRule: {
        ecType: '',
        ecReason: '',
        ecPoint: ''
      },
      rules: [],
      ecTypes: [{
        value: '0',
        label: '奖励'
      }, {
        value: '1',
        label: '惩罚'
      }],
      value: ''
    }
  },
  mounted() {
    this.initMyApplys();
  },
  methods: {
    deleteMany() {
      this.$confirm('此操作将永久删除【' + this.multipleSelection.length + '】条记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let ids = '?';
        this.multipleSelection.forEach(item => {
          ids += 'ids=' + item.id + '&';
        })
        this.deleteRequest("/system/basic/ecrule/" + ids).then(resp => {
          if (resp) {
            this.initMyApplys();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    updateCancel() {
      this.dialogVisible = false;
      this.value = '';
    },
    doUpdate() {
      this.updateRule.ecType = this.value;
      if ((this.updateRule.ecType == 0 && parseInt(this.updateRule.ecPoint) > 0) ||
          (this.updateRule.ecType == 1 && parseInt(this.updateRule.ecPoint) < 0)) {
        this.putRequest("/system/basic/ecrule/", this.updateRule).then(resp => {
          if (resp) {
            this.initMyApplys();
            this.dialogVisible = false;
          }
        });
      } else {
        this.$message.error("奖励的积分必须为正数,惩罚的积分必须为负数，请重试");
      }


    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    }
    ,
    showDetailView(data) {
      Object.assign(this.wfid, data.id);
      this.getRequest("/workflow/detail/"+data.id).then(resp=>{
        if (resp){
          this.detailApply=resp;
          console.log("detailApply:"+this.detailApply);
        }
      })
      this.dialogVisible = true;
    }
    ,
    deleteHandler(data) {
      this.$confirm('此操作将永久删除【' + data.ecReason + '】奖惩规则, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest("/system/basic/ecrule/" + data.id).then(resp => {
          if (resp) {
            this.initMyApplys();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    addEcRule() {
      if (this.ecRule.ecType && this.ecRule.ecPoint && this.ecRule.ecReason) {
        console.log(this.ecRule.ecType)
        if ((this.ecRule.ecType == '奖励' && this.ecRule.ecPoint > 0) ||
            (this.ecRule.ecType == '惩罚' && this.ecRule.ecPoint < 0)) {
          if (this.ecRule.ecType == '奖励') {
            this.ecRule.ecType = 0;
          } else {
            this.ecRule.ecType = 1;
          }
          this.postRequest("/system/basic/ecrule/", this.ecRule).then(resp => {
            if (resp) {
              this.initMyApplys();
            }
          });
        } else {
          this.$message.error("奖励的积分必须为正数,惩罚的积分必须为负数，请重试");
        }

      } else {
        this.$message.error("添加字段不可以为空!");
      }
    }
    ,
    initMyApplys() {
      this.loading = true;
      this.getRequest("/workflow/my/").then(resp => {
        this.loading = false;
        if (resp) {
          this.myApplys = resp;
          this.ecRule = {
            ecType: '',
            ecReason: '',
            ecPoint: ''
          }
        }
      })
    }
  }
}
</script>

<style scoped>

</style>