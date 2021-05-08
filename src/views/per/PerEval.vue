<template>
  <div>
    <div style="margin-top: 20px">
      <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                clearable
                @clear="initEmpEval"
                style="width: 350px;margin-right: 10px" v-model="outname"
                @keydown.enter.native="initEmpEval"></el-input>
      <el-button icon="el-icon-search" type="primary" @click="initEmpEval">
        搜索
      </el-button>

      <el-button icon="el-icon-plus" type="primary" @click="dialogVisible = true">
        添加考评记录
      </el-button>
    </div>

    <div style="margin-top: 10px">
      <el-table
          :data="empEval"
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
            prop="content"
            label="考评内容"
            width="240">
        </el-table-column>
        <el-table-column
            label="考评结果">
          <template slot-scope="scope">
            <el-tag size="small" type="success" v-if="scope.row.result===1">通过</el-tag>
            <el-tag size="small" type="danger" v-else>未通过</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="date"
            label="考评日期"
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
        title="添加员工考评记录"
        :visible.sync="dialogVisible"
        width="30%">

      <el-input placeholder="请选择员工" v-model="empSelection.name" style="width: 300px;margin-top: 10px">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllEmps"></el-button>
      </el-input>

      <el-input
          style="margin-top: 10px"
          type="textarea"
          :rows="2"
          autosize
          placeholder="请输入考评内容"
          v-model="textarea">
      </el-input>

      <div style="margin-top: 10px">
        <el-select v-model="addEmpEval.result" placeholder="请选择考评结果">
          <el-option label="通过" value="1"></el-option>
          <el-option label="未通过" value="0"></el-option>
        </el-select>
      </div>


      <el-date-picker
          style="margin-top: 10px"
          v-model="pickDate"
          align="right"
          type="date"
          placeholder="请选择考评日期"
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
            title="修改员工考评记录"
            :visible.sync="editdialogVisible"
            width="30%">

          <el-input  placeholder="请选择员工" v-model="updateEval.name" style="width: 300px;margin-top: 10px">
            <el-button icon="el-icon-search" slot="prepend" @click="getAllEmps"></el-button>
          </el-input>
          <el-input
              style="margin-top: 10px"
              type="textarea"
              :rows="2"
              autosize
              placeholder="请输入考评内容"
              v-model="updateEval.content">
          </el-input>
          <div style="margin-top: 10px">
            <el-select v-model="updateEval.result" placeholder="请选择考评结果">
              <el-option label="通过" value="1"></el-option>
              <el-option label="未通过" value="0"></el-option>
            </el-select>
          </div>
          <el-date-picker
              style="margin-top: 10px"
              v-model="updateEval.date"
              align="right"
              type="date"
              placeholder="请选择考评日期"
              :picker-options="pickerOptions">
          </el-date-picker>
          <el-input style="margin-top: 10px" placeholder="请输入备注内容" v-model="updateEval.remark">
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
  name: "PerEval",
  data() {
    return {
      outname: '',
      name: '',
      empEval: [],
      loading: false,
      outloading: false,
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
      textarea: '',
      remark: '',
      pickDate: '',
      multipleSelection: [],
      updateEval: {
        id: '',
        eid: '',
        date: '',
        content: '',
        result:'',
        remark: ''
      },
      addEmpEval: {
        eid: '',
        date: '',
        content: '',
        result:'',
        remark: ''
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
    this.initEmpEval();
  },
  methods: {
    doAddEmpEval() {
      if (this.empSelection.id && this.textarea && this.pickDate) {
        this.addEmpEval.eid = this.empSelection.id;
        this.addEmpEval.content = this.textarea;
        this.addEmpEval.date = this.pickDate;
        this.addEmpEval.remark = this.remark;
        this.postRequest("/personnel/eval/", this.addEmpEval).then(resp => {
          if (resp) {
            this.initEmpEval();
            this.addEmpEval = {
              eid: '',
              date: '',
              content: '',
              result:'',
              remark: ''
            };
            this.dialogVisible = false;
          }
        });
      } else {
        this.$message.error("字段不能为空");
      }
    },
    doUpdateEmpEval() {
      console.log('dobefore:'+this.updateEval.result);
      if (this.updateEval.result=='通过' || this.updateEval.result=='1'){
        this.updateEval.result='1';
      }else {
        this.updateEval.result='0';
      }
      console.log('do:'+this.updateEval.result);
      if (this.updateEval.id && this.updateEval.eid && this.updateEval.content && this.updateEval.date ) {
        this.putRequest("/personnel/eval/", this.updateEval).then(resp => {
          if (resp) {
            this.initEmpEval();
            this.updateEval = {
              id: '',
              eid: '',
              date: '',
              content: '',
              result:'',
              remark: ''
            };
            this.editdialogVisible = false;
          }
        });
      } else {
        this.$message.error("更新字段不能为空");
      }
    },
    addDialogOk() {
      this.doAddEmpEval();
      this.dialogCloseHandle();
    },
    addDialogCancel() {
      this.dialogCloseHandle();
      this.dialogVisible = false;
    },
    updateDialogOk() {
      this.doUpdateEmpEval();
      this.updateEval = {
        id: '',
        eid: '',
        date: '',
        content: '',
        result:'',
        remark: ''
      };
      this.editdialogVisible = false;
    },
    updateDialogCancel() {
      this.dialogCloseHandle();
      this.updateEval = {
        id: '',
        eid: '',
        trainDate: '',
        trainContent: '',
        remark: ''
      };
      this.editdialogVisible = false;
    },
    dialogCloseHandle() {
      this.empSelection = {};
      this.textarea = '';
      this.remark = '';
      this.pickDate = '';
      this.addEmpEval.result='';
    },
    showEditView(data) {
      Object.assign(this.updateEval, data);
      if (this.updateEval.result=='1'){
        this.updateEval.result='通过';
      }else {
        this.updateEval.result='未通过';
      }
      console.log('show:'+this.updateEval.result);
      this.editdialogVisible = true;
    },
    deleteHandler(data) {
      this.$confirm('此操作将永久删除此条考评记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest("/personnel/eval/" + data.id).then(resp => {
          if (resp) {
            this.initEmpEval();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    handleEmpSelectionChange(val) {
      console.log(val);
      this.empSelection = val;
      this.updateEval.name = val.name;
      this.updateEval.eid = val.id;
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
      this.initEmpEval();
    },
    outcurrentChange(currentPage) {
      this.outpage = currentPage;
      this.initEmpEval();
    },
    initEmpEval() {
      this.outloading = true;
      let url = '/personnel/eval/?page=' + this.outpage + '&size=' + this.outsize;
      if (this.outname) {
        url += "&name=" + this.outname;
      }
      this.getRequest(url).then(resp => {
        this.outloading = false;
        if (resp) {
          this.empEval = resp.list;
          this.total = resp.total;
        }
      })
    }
  }
}
</script>

<style scoped>

</style>