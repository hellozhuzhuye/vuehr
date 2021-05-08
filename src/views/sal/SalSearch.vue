<template>
  <div>
    <div>
      <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                clearable
                @clear="initSalaryAccountList"
                style="width: 350px;margin-right: 10px" v-model="ename"
                @keydown.enter.native="initSalaryAccountList"></el-input>
      <el-button icon="el-icon-search" type="primary" @click="initSalaryAccountList">
        搜索
      </el-button>
      <el-date-picker
          style="margin-left: 20px"
          v-model="beginDateScope"
          type="monthrange"
          value-format="yyyy-MM"
          range-separator="至"
          start-placeholder="开始月份"
          end-placeholder="结束月份">
      </el-date-picker>
    </div>
    <div style="margin-top: 10px">
      <el-table
          :data="accountData"
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
            prop="sname"
            width="170"
            align="left"
            label="工资账套">
        </el-table-column>
        <el-table-column
            prop="basicSalary"
            width="100"
            align="left"
            label="基本工资">
        </el-table-column>
        <el-table-column
            width="120"
            align="left"
            label="补贴及奖金总额">
          <template slot-scope="scope">
            <el-tag type="success" >{{scope.row.lunchSalary+scope.row.trafficSalary+scope.row.bonus}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            width="120"
            align="left"
            label="五险一金扣费总额">
          <template slot-scope="scope">
            <el-tag type="danger" >{{scope.row.pension
            +scope.row.medical
            +scope.row.unemploy
            +scope.row.jobInjury
            +scope.row.birth
            +scope.row.accumulationFund}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="attendanceDeduction"
            width="100"
            align="left"
            label="考勤扣费">
        </el-table-column>
        <el-table-column
            prop="netSalary"
            width="100"
            align="left"
            label="实发工资">
        </el-table-column>
        <el-table-column
            prop="currentMonth"
            width="100"
            align="left"
            label="当前月份">
        </el-table-column>

        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button
                size="mini"
                type="primary"
                @click="showDetail(scope.row.id)">详细
            </el-button>
          </template>
        </el-table-column>

      </el-table>
      <div style="display: flex;justify-content: flex-end;margin-top: 20px">
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
        title="详细信息"
        :visible.sync="detailVisible"
        width="30%">
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.ename">
        <template slot="prepend">姓名</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.sname">
        <template slot="prepend">工资账套</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.basicSalary">
        <template slot="prepend">基本工资</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.lunchSalary">
        <template slot="prepend">餐费补贴</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.trafficSalary">
        <template slot="prepend">交通补贴</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.pension">
          <template slot="prepend">养老保险扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.medical">
        <template slot="prepend">医疗保险扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.unemploy">
        <template slot="prepend">失业保险扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.pension">
        <template slot="prepend">养老保险扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.jobInjury">
        <template slot="prepend">工伤保险扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.birth">
        <template slot="prepend">生育保险扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.accumulationFund">
        <template slot="prepend">公积金扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.bonus">
        <template slot="prepend">绩效奖金</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.attendanceDeduction">
        <template slot="prepend">考勤扣费</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.netSalary">
        <template slot="prepend">实发工资</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="accountDetail.currentMonth">
        <template slot="prepend">当前月份</template>
      </el-input>
      <span slot="footer" class="dialog-footer">
<!--    <el-button @click="detailVisible = false">取 消</el-button>-->
    <el-button type="primary" @click="detailVisible = false">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "SalSearch",
  data() {
    return {
      accountData: [],
      accountDetail:{},
      ename: "",
      page: 1,
      size: 10,
      total: 0,
      beginDateScope: null,
      detailVisible: false
    }
  },
  methods: {
    showDetail(id){
      let url = '/salary/search/' + id;
      this.getRequest(url).then(resp => {
        if (resp) {
          this.accountDetail = resp.obj;
          this.detailVisible=true
        }
      })
    },
    sizeChange(currentSize) {
      this.size = currentSize;
      this.initSalaryAccountList();
    },
    currentChange(currentPage) {
      this.page = currentPage;
      this.initSalaryAccountList();
    },
    initSalaryAccountList() {
      let url = '/salary/search/?pageNum=' + this.page + '&pageSize=' + this.size;
      if (this.ename) {
        url += "&ename=" + this.ename;
      }
      if (this.beginDateScope) {
        url += '&startTime=' + this.beginDateScope[0] + "-01";
        url += '&endTime=' + this.beginDateScope[1] + "-28";
      }
      this.getRequest(url).then(resp => {
        if (resp) {
          this.accountData = resp.obj.list;
          this.total = resp.obj.total;
        }
      })
    }
  },
  mounted() {
    this.initSalaryAccountList();
  }
}
</script>

<style scoped>

</style>