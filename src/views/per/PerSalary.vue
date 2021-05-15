<template>
    <div>
      <div style="margin-bottom: 10px">
        <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                  clearable
                  @clear="initAdjustSalary"
                  style="width: 350px;margin-right: 10px" v-model="ename"
                  @keydown.enter.native="initAdjustSalary" ></el-input>
        <el-button icon="el-icon-search" type="primary" @click="initAdjustSalary" >
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

      <el-input placeholder="请选择员工" v-model="empSelection.name" style="width: 300px;">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllEmps"></el-button>
      </el-input>
      <el-input size="small" v-model="addAdjust.afterSalary" style="width: 300px;" prefix-icon="el-icon-plus"
                placeholder="请输入调后薪资倍数">
      </el-input>
      <el-input size="small" v-model="addAdjust.reason" style="width: 300px;" prefix-icon="el-icon-plus"
                placeholder="请输入调薪原因">
      </el-input>
      <el-input size="small" v-model="addAdjust.remark" style="width: 300px;" prefix-icon="el-icon-plus"
                placeholder="请输入备注">
      </el-input>

      <el-button icon="el-icon-plus" type="primary" size="small" @click="doAddAdjust">添加奖惩记录</el-button>


      <div style="margin-top: 10px">
        <el-table
            :data="logData"
            stripe
            border
            element-loading-text="正在加载..."
            element-loading-spinner="el-icon-loading"
            element-loading-background="rgba(0, 0, 0, 0.8)"
            style="width: 100%">
          <el-table-column
              prop="id"
              label="序号"
              align="left"
              width="60">
          </el-table-column>
          <el-table-column
              prop="ename"
              align="left"
              label="姓名"
              width="100">
          </el-table-column>
          <el-table-column
              prop="beforeSalary"
              width="120"
              align="left"
              label="调前薪资倍数">
          </el-table-column>
          <el-table-column
              prop="afterSalary"
              width="200"
              align="left"
              label="调后薪资倍数">
          </el-table-column>
          <el-table-column
              prop="reason"
              width="200"
              align="left"
              label="调薪原因">
          </el-table-column>
          <el-table-column
              prop="asDate"
              width="200"
              align="left"
              label="调薪日期">
          </el-table-column>
          <el-table-column
              prop="remark"
              width="200"
              align="left"
              label="备注">
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
        name: "PerSalary",
      data(){
        return {
          selectEmpsVisible:false,
          emps:[],
          loading:false,
          keyword:'',
          empSelection:{},
          addAdjust:{
            afterSalary:'',
            reason:'',
            remark:''
          },
          logData:[],
          ename: "",
          page: 1,
          size: 10,
          total: 0,
          beginDateScope: null
        }
      },
      methods:{
        handleEmpSelectionChange(val){
          this.empSelection=val;
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
        doAddAdjust() {
          if (this.empSelection.id && this.empEcRuleMultipleSelection.id){
            this.addAdjust.eid=this.empSelection.id;
            this.postRequest("/personnel/salary/", this.addAdjust).then(resp => {
              if (resp) {
                this.initAdjustSalary();
                this.addAdjust={
                  afterSalary:'',
                      reason:'',
                      remark:''
                };
              }
            });
          }
          else {
            this.$message.error("字段不能为空");
          }
        },
        sizeChange(currentSize) {
          this.size = currentSize;
          this.initAdjustSalary();
        },
        currentChange(currentPage) {
          this.page = currentPage;
          this.initAdjustSalary();
        },
        initAdjustSalary(){
          let url='/personnel/salary/?pageNum=' + this.page + '&pageSize=' + this.size;
          if (this.ename){
            url += "&ename=" + this.ename;
          }
          if (this.beginDateScope) {
            url += '&startTime=' + this.beginDateScope[0]+" 00:00:00";
            url += '&endTime=' + this.beginDateScope[1]+" 23:59:59";
          }
          this.getRequest(url).then(resp => {
            if (resp) {
              this.logData = resp.obj.list;
              this.total = resp.obj.total;
            }
          })
        }
      },
      mounted(){
        this.initAdjustSalary();
      }
    }
</script>

<style scoped>

</style>