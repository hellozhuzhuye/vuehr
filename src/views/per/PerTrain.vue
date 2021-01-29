<template>
  <div>
    <div style="margin-top: 20px">
      <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                clearable
                @clear="initEmpTrain"
                style="width: 350px;margin-right: 10px" v-model="name"
                @keydown.enter.native="initEmpTrain"></el-input>
      <el-button icon="el-icon-search" type="primary" @click="initEmpTrain">
        搜索
      </el-button>

      <el-button icon="el-icon-plus" type="primary" @click="dialogVisible = true">
        添加培训记录
      </el-button>
    </div>

    <div style="margin-top: 10px">
      <el-table
          :data="empTrain"
          border
          v-loading="outloading"
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
            prop="trainContent"
            label="培训内容"
            width="440">
        </el-table-column>
        <el-table-column
            prop="trainDate"
            label="培训日期"
            width="150">
        </el-table-column>
        <el-table-column
            prop="remark"
            label="备注"
            width="200">
        </el-table-column>
        <el-table-column
            label="操作">
          <template slot-scope="scope">
            <el-button size="small" @click="showEditView(scope.row)">编辑</el-button>
            <el-button size="small" type="danger" @click="deleteHandler(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="display: flex;justify-content: flex-start;margin-top: 10px">
        <el-pagination
            background
            @current-change="outcurrentChange"
            @size-change="outsizeChange"
            layout="sizes, prev, pager, next, jumper, ->, total, slot"
            :total="outtotal">
        </el-pagination>
      </div>
    </div>
    <el-dialog
        title="添加员工培训记录"
        :visible.sync="dialogVisible"
        width="30%">

        <el-input  placeholder="请选择员工" v-model="empSelection.name" style="width: 300px;margin-top: 10px">
          <el-button icon="el-icon-search" slot="prepend" @click="getAllEmps"></el-button>
        </el-input>
        <el-input
            style="margin-top: 10px"
            type="textarea"
            :rows="2"
            autosize
            placeholder="请输入培训内容"
            v-model="textarea">
        </el-input>
        <el-date-picker
            style="margin-top: 10px"
            v-model="pickDate"
            align="right"
            type="date"
            placeholder="请选择培训日期"
            :picker-options="pickerOptions">
        </el-date-picker>
        <el-input style="margin-top: 10px" placeholder="请输入备注内容" v-model="remark">
          <template slot="prepend">备注</template>
        </el-input>



      <span slot="footer" class="dialog-footer">
    <el-button @click="addDialogCancel">取 消</el-button>
    <el-button type="primary" @click="addDialogOk">确 定</el-button>
  </span>
    </el-dialog>

    <el-dialog
        title="修改员工培训记录"
        :visible.sync="editdialogVisible"
        width="30%">

      <el-input  placeholder="请选择员工" v-model="updateTrain.name" style="width: 300px;margin-top: 10px">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllEmps"></el-button>
      </el-input>
      <el-input
          style="margin-top: 10px"
          type="textarea"
          :rows="2"
          autosize
          placeholder="请输入培训内容"
          v-model="updateTrain.trainContent">
      </el-input>
      <el-date-picker
          style="margin-top: 10px"
          v-model="updateTrain.trainDate"
          align="right"
          type="date"
          placeholder="请选择培训日期"
          :picker-options="pickerOptions">
      </el-date-picker>
      <el-input style="margin-top: 10px" placeholder="请输入备注内容" v-model="updateTrain.remark">
        <template slot="prepend">备注</template>
      </el-input>

      <span slot="footer" class="dialog-footer">
    <el-button @click="updateDialogCancel">取 消</el-button>
    <el-button type="primary" @click="updateDialogOk">确 定</el-button>
  </span>
    </el-dialog>

    <el-dialog
        title="选择员工"
        :visible.sync="selectEmpsVisible"
        width="40%">
      <div>
        <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                  clearable
                  @clear="getAllEmps"
                  style="width: 350px;margin-right: 10px" v-model="name"
                  @keydown.enter.native="getAllEmps"></el-input>
        <el-button icon="el-icon-search" type="primary" @click="getAllEmps">
          搜索
        </el-button>
      </div>
      <el-table
          :data="emps"
          highlight-current-row
          @current-change="handleEmpSelectionChange"
          border
          v-loading="loading"
          element-loading-text="正在加载..."
          element-loading-spinner="el-icon-loading"
          element-loading-background="rgba(0, 0, 0, 0.8)"
          size="small"
          style="width: 80%;margin-top: 10px">
        <el-table-column
            prop="id"
            label="编号"
            width="75">
        </el-table-column>
        <el-table-column
            prop="workID"
            label="工号"
            width="200">
        </el-table-column>
        <el-table-column
            prop="name"
            label="姓名"
            width="147">
        </el-table-column>
        <el-table-column
            prop="department.name"
            label="部门"
            width="147">
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
      <span slot="footer" class="dialog-footer">
    <el-button @click="selectEmpsVisible = false">取 消</el-button>
    <el-button type="primary" @click="selectEmp">确 定</el-button>
    </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "PerTrain",
  data() {
    return {
      outname: '',
      name:'',
      empTrain: [],
      loading: false,
      outloading:false,
      page: 1,
      size: 10,
      total: 0,
      outpage: 1,
      outsize: 10,
      outtotal: 0,
      dialogVisible: false,
      editdialogVisible: false,
      selectEmpsVisible: false,
      updateselectEmpsVisible: false,
      emps: [],
      empSelection: {},
      textarea:'',
      remark:'',
      pickDate:'',
      multipleSelection: [],
      updateTrain: {
        id:'',
        eid:'',
        trainDate:'',
        trainContent:'',
        remark:''
      },
      addEmpTrain:{
        eid:'',
        trainDate:'',
        trainContent:'',
        remark:''
      },
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() > Date.now();
        },
        shortcuts: [{
          text: '今天',
          onClick(picker) {
            picker.$emit('pick', new Date());
          }
        }, {
          text: '昨天',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24);
            picker.$emit('pick', date);
          }
        }, {
          text: '一周前',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
            picker.$emit('pick', date);
          }
        }]
      },
    }
  },
  mounted() {
    this.initEmpTrain();
  },
  methods: {
    doAddEmpTrain() {
      if (this.empSelection.id && this.textarea && this.pickDate){
        this.addEmpTrain.eid=this.empSelection.id;
        this.addEmpTrain.trainContent=this.textarea;
        this.addEmpTrain.trainDate=this.pickDate;
        this.addEmpTrain.remark=this.remark;
        this.postRequest("/personnel/train/", this.addEmpTrain).then(resp => {
          if (resp) {
            this.initEmpTrain();
            this.addEmpTrain={
                  eid:'',
                  trainDate:'',
                  trainContent:'',
                  remark:''
            };
            this.dialogVisible=false;
          }
        });
      }
      else {
        this.$message.error("字段不能为空");
      }
    },
    doUpdateEmpTrain() {
      // console.log(this.updateTrain);
      if (this.updateTrain.id && this.updateTrain.eid && this.updateTrain.trainContent && this.updateTrain.trainDate){
        this.putRequest("/personnel/train/", this.updateTrain).then(resp => {
          if (resp) {
            this.initEmpTrain();
            this.updateTrain={
              id:'',
              eid:'',
              trainDate:'',
              trainContent:'',
              remark:''
            };
            this.editdialogVisible=false;
          }
        });
      }
      else {
        this.$message.error("更新字段不能为空");
      }
    },
    addDialogOk(){
      this.doAddEmpTrain();
      this.dialogCloseHandle();
    },
    addDialogCancel(){
      this.dialogCloseHandle();
      this.dialogVisible=false;
    },
    updateDialogOk(){
      this.doUpdateEmpTrain();
      this.updateTrain= {
        id:'',
            eid:'',
            trainDate:'',
            trainContent:'',
            remark:''
      };
      this.editdialogVisible=false;
    },
    updateDialogCancel(){
      this.dialogCloseHandle();
      this.updateTrain= {
        id:'',
        eid:'',
        trainDate:'',
        trainContent:'',
        remark:''
      };
      this.editdialogVisible=false;
    },
    dialogCloseHandle(){
      this.empSelection={};
      this.textarea='';
      this.remark='';
      this.pickDate='';
    },
    showEditView(data) {
      Object.assign(this.updateTrain, data);
      this.editdialogVisible = true;
    }
    ,
    deleteHandler(data) {
      this.$confirm('此操作将永久删除此条培训记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest("/personnel/train/" + data.id).then(resp => {
          if (resp) {
            this.initEmpTrain();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    handleEmpSelectionChange(val){
      console.log(val);
      this.empSelection=val;
      this.updateTrain.name=val.name;
      this.updateTrain.eid=val.id;
    },
    selectEmp() {
      this.selectEmpsVisible = false;
    },
    getAllEmps() {
      this.selectEmpsVisible = true;
      let url = '/employee/basic/?page=' + this.page + '&size=' + this.size;
      if (this.name) {
        url += "&name=" + this.name;
      }
      this.getRequest(url).then(resp => {
        this.loading = false;
        if (resp) {
          console.log(resp.data);
          this.emps = resp.data;
          this.total = resp.total;
        }
      });
    },
    sizeChange(currentSize) {
      this.size = currentSize;
      this.getAllEmps();
    },
    currentChange(currentPage) {
      this.page = currentPage;
      this.getAllEmps();
    },
    outsizeChange(currentSize) {
      this.outsize = currentSize;
      this.initEmpTrain();
    },
    outcurrentChange(currentPage) {
      this.outpage = currentPage;
      this.initEmpTrain();
    },
    initEmpTrain() {
      this.outloading = true;
      let url = '/personnel/train/?page=' + this.outpage + '&size=' + this.outsize;
      if (this.name) {
        url += "&name=" + this.outname;
      }
      this.getRequest(url).then(resp => {
        this.outloading = false;
        if (resp) {
          this.empTrain = resp.list;
          this.total = resp.total;
        }
      })
    }
  }
}
</script>

<style scoped>

</style>