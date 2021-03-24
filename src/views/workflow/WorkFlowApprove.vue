<template>
  <div>
    <div style="margin-top: 10px">
      <el-table
          :data="approves"
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
            prop="name"
            label="申请人"
            width="100">
        </el-table-column>
        <el-table-column
            prop="typeName"
            label="申请类型"
            width="120">
        </el-table-column>
        <el-table-column
            prop="selectDateTime"
            label="选择时间"
            width="145">
        </el-table-column>
        <el-table-column
            prop="content"
            label="申请内容"
            width="200">
        </el-table-column>
        <el-table-column
            label="申请文件"
            width="200">
          <template slot-scope="scope">
            <el-link type="primary" @click="urlClick(scope.row.url)">点此下载</el-link>
          </template>
        </el-table-column>
        <el-table-column
            prop="createTime"
            label="申请时间"
            width="200">
        </el-table-column>
        <el-table-column
            label="申请状态"
            width="200">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status===0">未审核</el-tag>
            <el-tag type="success" v-else-if="scope.row.status===1">批准</el-tag>
            <el-tag type="danger" v-else-if="scope.row.status===2">未批准</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="approveContent"
            label="审核回复"
            width="200">
        </el-table-column>
        <el-table-column
            prop="approveTime"
            label="审核时间"
            width="200">
        </el-table-column>
        <el-table-column
            fixed="right"
            label="操作"
            width="150">
          <template slot-scope="scope">
            <el-button size="small" type="primary" @click="showApprove(scope.row.id,1)">批准</el-button>
            <el-button size="small" type="danger" @click="showApprove(scope.row.id,2)">拒绝</el-button>
          </template>
        </el-table-column>
      </el-table>


    </div>

    <el-dialog
        title="流程审批"
        :visible.sync="dialogVisible"
        width="30%">
      <div>
        <table>
          <tr>
            <td>
              <el-tag>审核回复</el-tag>
            </td>
            <td>
              <el-input size="small" v-model="content"></el-input>
            </td>
          </tr>
        </table>
      </div>
      <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="approveCancel">取 消</el-button>
    <el-button size="small" type="primary" @click="doApprove">确 定</el-button>
  </span>
    </el-dialog>
  </div>

</template>

<script>
export default {
  name: "WorkFlowApprove",
  data() {
    return {
      dialogVisible: false,
      loading: false,
      multipleSelection: [],
      content: "",
      status: 0,
      id: 0,
      approves: [],
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
    this.initApprove();
  },
  methods: {
    urlClick(url){
      window.open(url);
    },
    showApprove(id, status) {
      this.id = id;
      this.status = status;
      if (status == 1) {
        this.content = "予以批准";
      } else {
        this.content = "不予批准";
      }
      this.dialogVisible = true;
    },
    approveCancel() {
      this.content = "";
      this.id = 0;
      this.status = 0;
      this.dialogVisible = false;
    },
    doApprove(){
      this.getRequest("/workflow/processApproval",{"id":this.id,"status":this.status,"content":this.content}).then(resp => {
        if (resp) {
          this.dialogVisible=false;
          this.initApprove();
        }
      });
    },
    initApprove() {
      this.loading = true;
      this.getRequest("/workflow/approve/").then(resp => {
        this.loading = false;
        if (resp) {
          this.approves = resp;
        }
      })
    }
  }
}

</script>


<style scoped>

</style>