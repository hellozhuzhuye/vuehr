<template>
  <div>
    <div>

      <el-select v-model="ecRule.ecType" slot="prepend" placeholder="请选择奖惩类型">
        <el-option label="奖励" value="奖励"></el-option>
        <el-option label="惩罚" value="惩罚"></el-option>
      </el-select>
      <el-input size="small" v-model="ecRule.ecReason" style="width: 300px;" prefix-icon="el-icon-plus"
                placeholder="请输入奖惩原因"></el-input>
      <el-input size="small" v-model="ecRule.ecPoint" style="width: 300px;" prefix-icon="el-icon-plus"
                placeholder="请输入奖惩积分"></el-input>

      <el-button icon="el-icon-plus" type="primary" size="small" @click="addEcRule">添加</el-button>
    </div>
    <div style="margin-top: 10px">
      <el-table
          :data="rules"
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
        <!--        <el-table-column-->
        <!--            label="是否启用">-->
        <!--          <template slot-scope="scope">-->
        <!--            <el-tag type="success" v-if="scope.row.enabled">已启用</el-tag>-->
        <!--            <el-tag type="danger" v-else>未启用</el-tag>-->
        <!--          </template>-->
        <!--        </el-table-column>-->
        <el-table-column
            label="操作">
          <template slot-scope="scope">
            <el-button size="small" @click="showEditView(scope.row)">编辑</el-button>
            <el-button size="small" type="danger" @click="deleteHandler(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-button type="danger" size="small" style="margin-top: 10px" :disabled="multipleSelection.length==0"
                 @click="deleteMany">批量删除
      </el-button>

    </div>
    <el-dialog
        title="修改奖惩规则"
        :visible.sync="dialogVisible"
        width="30%">
      <div>
        <table>
          <tr>
            <td>
              <el-tag>奖惩类型</el-tag>
            </td>
            <td>
              <el-select v-model="value" placeholder="请选择">
              <el-option
                  v-for="item in ecTypes"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
              </el-option>
            </el-select>

            </td>
          </tr>
          <tr>
            <td>
              <el-tag>奖惩原因</el-tag>
            </td>
            <td>
              <el-input size="small" v-model="updateRule.ecReason"></el-input>
            </td>
          </tr>
          <tr>
            <td>
              <el-tag>奖惩积分</el-tag>
            </td>
            <td>
              <el-input size="small" v-model="updateRule.ecPoint"></el-input>
            </td>
          </tr>
        </table>
      </div>
      <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="updateCancel">取 消</el-button>
    <el-button size="small" type="primary" @click="doUpdate">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "EcMana",
  data() {
    return {
      dialogVisible: false,
      loading: false,
      multipleSelection: [],
      updateRule: {
        ecType: '',
        ecReason: '',
        ecPoint: 0
      },
      ecRule: {
        ecType: '',
        ecReason: '',
        ecPoint: ''
      },
      rules: [],
      ecTypes: [{
        value:'0',
        label:'奖励'
      },{
        value:'1',
        label:'惩罚'
      }],
      value:''
    }
  },
  mounted() {
    this.initEcRules();
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
            this.initEcRules();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    updateCancel(){
      this.dialogVisible = false;
      this.value='';
    },
    doUpdate() {
      console.log(this.updateRule);
      this.updateRule.ecType=this.value;
      if ((this.updateRule.ecType == 0 && parseInt(this.updateRule.ecPoint) > 0) ||
          (this.updateRule.ecType == 1 && parseInt(this.updateRule.ecPoint) < 0)) {
        this.putRequest("/system/basic/ecrule/", this.updateRule).then(resp => {
          if (resp) {
            this.initEcRules();
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
    showEditView(data) {
      Object.assign(this.updateRule, data);
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
            this.initEcRules();
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
    addEcRule() {
      if (this.ecRule.ecType && this.ecRule.ecPoint && this.ecRule.ecReason) {
        console.log(this.ecRule.ecType)
        if ((this.ecRule.ecType == '奖励' && this.ecRule.ecPoint > 0) ||
            (this.ecRule.ecType == '惩罚' && this.ecRule.ecPoint < 0)) {
          if (this.ecRule.ecType=='奖励'){
            this.ecRule.ecType=0;
          }else {
            this.ecRule.ecType=1;
          }
          this.postRequest("/system/basic/ecrule/", this.ecRule).then(resp => {
            if (resp) {
              this.initEcRules();
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
    initEcRules() {
      this.loading = true;
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
    }
  }
}

</script>

<style scoped>

</style>