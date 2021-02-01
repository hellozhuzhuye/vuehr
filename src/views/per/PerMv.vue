<template>
  <div>
    <div style="margin-top: 20px">
      <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                clearable
                @clear="initEmpRemove"
                style="width: 350px;margin-right: 10px" v-model="name"
                @keydown.enter.native="initEmpRemove"></el-input>
      <el-button icon="el-icon-search" type="primary" @click="initEmpRemove">
        搜索
      </el-button>
    </div>

    <div style="margin-top: 10px">
      <el-table
          :data="empRemove"
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
            prop="ename"
            label="姓名"
            width="120">
        </el-table-column>
        <el-table-column
            prop="dbeforename"
            label="调动前部门"
            width="120">
        </el-table-column>
        <el-table-column
            prop="pbeforename"
            label="调动前职位"
            width="120">
        </el-table-column>
        <el-table-column
            prop="daftername"
            label="调动后部门"
            width="120">
        </el-table-column>
        <el-table-column
            prop="paftername"
            label="调动后部门"
            width="120">
        </el-table-column>
        <el-table-column
            prop="reason"
            label="调动原因"
            width="150">
        </el-table-column>
        <el-table-column
            prop="removeDate"
            label="调动时间"
            width="120">
        </el-table-column>
        <el-table-column
            prop="remark"
            label="备注"
            width="140">
        </el-table-column>
        <el-table-column
            label="操作"
            width="100">
          <template slot-scope="scope">
            <el-button size="small" @click="showEditView(scope.row)">编辑</el-button>
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
    <el-dialog
        title="修改员工调动记录备注及原因"
        :visible.sync="editdialogVisible"
        width="30%">
      <el-input
          style="margin-top: 10px"
          type="textarea"
          :rows="2"
          autosize
          placeholder="请输入调动原因"
          v-model="updateRemove.reason">
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入备注内容" v-model="updateRemove.remark">
        <template slot="prepend">备注</template>
      </el-input>

      <span slot="footer" class="dialog-footer">
    <el-button @click="updateDialogCancel">取 消</el-button>
    <el-button type="primary" @click="updateDialogOk">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "PerMv",
  data() {
    return {
      name: '',
      empRemove: [],
      loading: false,
      page: 1,
      size: 10,
      total: 0,
      updateRemove: {
        reason: '',
        remark: ''
      },
      editdialogVisible: false
    }
  },
  mounted() {
    this.initEmpRemove();
  },
  methods: {
    updateDialogOk() {
      this.doUpdateEmpRemove();
      this.updateTrain = {};
      this.editdialogVisible = false;
    },
    updateDialogCancel() {
      this.updateTrain = {};
      this.editdialogVisible = false;
    },
    showEditView(data) {
      Object.assign(this.updateRemove, data);
      this.editdialogVisible = true;
    },
    doUpdateEmpRemove() {
      this.putRequest("/personnel/remove/", this.updateRemove).then(resp => {
        if (resp) {
          this.initEmpRemove();
          this.updateRemove = {
            reason: '',
            remark: ''
          };
          this.editdialogVisible = false;
        }
      });

    },
    sizeChange(currentSize) {
      this.size = currentSize;
      this.initEmpRemove();
    },
    currentChange(currentPage) {
      this.page = currentPage;
      this.initEmpRemove();
    },
    initEmpRemove() {
      this.loading = true;
      let url = '/personnel/remove/?page=' + this.page + '&size=' + this.size;
      ;
      if (this.name) {
        url += "&name=" + this.name;
      }
      this.getRequest(url).then(resp => {
        this.loading = false;
        if (resp) {
          this.empRemove = resp.list;
          this.total = resp.total;
        }
      })
    }
  }
}
</script>

<style scoped>

</style>