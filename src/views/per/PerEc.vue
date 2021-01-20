<template>
  <div>
    <div>

      <!--      <el-select v-model="ecRule.ecType" slot="prepend" placeholder="请选择奖惩类型">-->
      <!--        <el-option label="奖励" value="奖励"></el-option>-->
      <!--        <el-option label="惩罚" value="惩罚"></el-option>-->
      <!--      </el-select>-->
      <!--      <el-input size="small" v-model="ecRule.ecReason" style="width: 300px;" prefix-icon="el-icon-plus"-->
      <!--                placeholder="请输入奖惩原因"></el-input>-->
      <!--      <el-input size="small" v-model="ecRule.ecPoint" style="width: 300px;" prefix-icon="el-icon-plus"-->
      <!--                placeholder="请输入奖惩积分"></el-input>-->
      <el-input placeholder="请选择奖惩规则" v-model="empEcRuleMultipleSelection.ecReason" style="width: 300px;">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllEcRules"></el-button>
      </el-input>
      <el-input placeholder="请选择员工" v-model="empSelection.name" style="width: 300px;">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllEmps"></el-button>
      </el-input>
      <el-input size="small" v-model="addEmpEc.remark" style="width: 300px;" prefix-icon="el-icon-plus"
                placeholder="请输入备注">
      </el-input>
     <el-button icon="el-icon-plus" type="primary" size="small" @click="doAddEmpEc">添加奖惩记录</el-button>
      <div style="margin-top: 20px">
        <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                  clearable
                  @clear="initEmpEcs"
                  style="width: 350px;margin-right: 10px" v-model="name"
                  @keydown.enter.native="initEmpEcs" ></el-input>
      <el-button icon="el-icon-search" type="primary" @click="initEmpEcs" >
        搜索
      </el-button>
      </div>

    </div>
    <div style="margin-top: 10px">
      <el-table
          :data="EmpEcs"
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
            prop="workID"
            label="工号"
            width="100">
        </el-table-column>
        <el-table-column
            prop="name"
            label="姓名"
            width="100">
        </el-table-column>
        <el-table-column
            label="奖惩类型"
            width="100">
          <template slot-scope="scope">
            <el-tag type="success" v-if="scope.row.ecType===0">奖励</el-tag>
            <el-tag type="danger" v-else>惩罚</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="ecPoint"
            label="奖惩积分"
            width="100">
        </el-table-column>
        <el-table-column
            prop="ecReason"
            label="奖惩原因"
            width="150">
        </el-table-column>
        <el-table-column
            prop="beforePoint"
            label="奖惩前积分"
            width="100">
        </el-table-column>
        <el-table-column
            prop="afterPoint"
            label="奖惩后积分"
            width="100">
        </el-table-column>
        <el-table-column
            prop="ecDate"
            label="奖惩日期"
            width="150">
        </el-table-column>
        <el-table-column
            prop="remark"
            label="备注"
            width="200">
        </el-table-column>
        <el-table-column
            label="操作"
            width="200">
          <template slot-scope="scope">
            <el-button size="small" type="danger" @click="deleteHandler(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-button type="danger" size="small" style="margin-top: 10px" :disabled="multipleSelection.length==0"
                 @click="deleteMany">批量删除
      </el-button>

    </div>
    <el-dialog
        title="选择奖惩规则"
        :visible.sync="selectRulesVisible"
        width="30%">
      <el-table
          :data="rules"
          highlight-current-row
          @current-change="handleEmpRuleSelectionChange"
          border
          v-loading="loading"
          element-loading-text="正在加载..."
          element-loading-spinner="el-icon-loading"
          element-loading-background="rgba(0, 0, 0, 0.8)"
          size="small"
          style="width: 80%">

        <el-table-column
            prop="id"
            label="编号"
            width="55">
        </el-table-column>
        <el-table-column
            label="奖惩类型"
            width="150">
          <template slot-scope="scope">
            <el-tag type="success" v-if="scope.row.ecType===0">奖励</el-tag>
            <el-tag type="danger" v-else>惩罚</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="ecPoint"
            label="奖惩积分">
        </el-table-column>
        <el-table-column
            prop="ecReason"
            label="奖惩原因">
        </el-table-column>
      </el-table>
      <span slot="footer" class="dialog-footer">
    <el-button @click="selectRulesVisible = false">取 消</el-button>
    <el-button type="primary" @click="selectEmpEc">确 定</el-button>
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
                  style="width: 350px;margin-right: 10px" v-model="keyword"
                  @keydown.enter.native="getAllEmps" ></el-input>
        <el-button icon="el-icon-search" type="primary" @click="getAllEmps" >
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
  name: "PerEc",
  data() {
    return {
      name:'',
      selectRulesVisible: false,
      addEmpEc: {
        eid: '',
        ecid: '',
        remark:''
      },
      loading: false,
      multipleSelection: [],
      empEcRuleMultipleSelection: {},
      empSelection:{},
      updateRule: {
        ecType: '',
        ecReason: '',
        ecPoint: 0
      },
      page:1,
      size:10,
      keyword:'',
      emps:[],
      total:0,
      rules: [],
      selectEmpsVisible:false,
      ecRule: {
        ecType: '',
        ecReason: '',
        ecPoint: ''
      },
      EmpEcs: [],
      ecTypes: [{
        value: '0',
        label: '奖励'
      }, {
        value: '1',
        label: '惩罚'
      }]
    }
  },
  mounted() {
    this.initEmpEcs();
  },
  methods: {
    currentChange(currentPage){
      console.log(currentPage);
      this.page = currentPage;
      this.getAllEmps();
    },
    sizeChange(currentSize){
      this.size = currentSize;
      this.getAllEmps();
    },
    selectEmpEc(){
      this.selectRulesVisible=false;

    },
    selectEmp(){
      this.selectEmpsVisible=false;
    },
    getAllEmps(){
      this.selectEmpsVisible=true;
      let url = '/employee/basic/?page=' + this.page + '&size=' + this.size;
      if (this.keyword){
        url += "&name=" + this.keyword;
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
    getAllEcRules() {
      this.selectRulesVisible = true;
      this.getRequest("/system/basic/ecrule/").then(resp => {
        this.loading = false;
        if (resp) {
          this.rules = resp;
          this.ecRule = {
            ecType: '',
            ecReason: '',
            ecPoint: ''
          }
        }
      })
    },
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
            this.initEmpEcs();
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

    },
    handleEmpSelectionChange(val){
      this.empSelection=val;
    },
    handleEmpRuleSelectionChange(val){
      this.empEcRuleMultipleSelection=val;
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    deleteHandler(data) {
      this.$confirm('此操作将永久删除此奖惩记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest("/personnel/ec/" + data.id).then(resp => {
          if (resp) {
            this.initEmpEcs();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    }
    ,
    doAddEmpEc() {
      if (this.empSelection.id && this.empEcRuleMultipleSelection.id){
        this.addEmpEc.eid=this.empSelection.id;
        this.addEmpEc.ecid=this.empEcRuleMultipleSelection.id;
        this.postRequest("/personnel/ec/", this.addEmpEc).then(resp => {
          if (resp) {
            this.initEmpEcs();
            this.addEmpEc.remark='';
          }
        });
      }
      else {
        this.$message.error("字段不能为空");
      }
    },
    initEmpEcs() {
      this.loading = true;
      let url='/personnel/ec/';
      if (this.name){
        url += "?name=" + this.name;
      }
      this.getRequest(url).then(resp => {
        this.loading = false;
        if (resp) {
          this.EmpEcs = resp;
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