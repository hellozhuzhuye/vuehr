<template>
    <div>
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
            width="120"
            align="left"
            label="工资账套">
        </el-table-column>
        <el-table-column
            prop="basicSalary"
            width="200"
            align="left"
            label="基本工资">
        </el-table-column>
        <el-table-column
            width="200"
            align="left"
            label="补贴及奖金总额">
          <template slot-scope="scope">
            <el-tag type="success" >{{scope.row.lunchSalary+scope.row.trafficSalary+scope.row.bonus}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            width="200"
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
            prop="lunchSalary"
            width="200"
            align="left"
            label="餐费补贴">
        </el-table-column>
        <el-table-column
            prop="pension"
            width="200"
            align="left"
            label="养老保险扣费">
        </el-table-column>
        <el-table-column
            prop="medical"
            width="200"
            align="left"
            label="医疗保险扣费">
        </el-table-column>
        <el-table-column
            prop="unemploy"
            width="200"
            align="left"
            label="失业保险扣费">
        </el-table-column>
        <el-table-column
            prop="jobInjury"
            width="200"
            align="left"
            label="工伤保险扣费">
        </el-table-column>
        <el-table-column
            prop="birth"
            width="200"
            align="left"
            label="生育保险扣费">
        </el-table-column>
        <el-table-column
            prop="accumulationFund"
            width="200"
            align="left"
            label="公积金扣费">
        </el-table-column>
        <el-table-column
            prop="bonus"
            width="200"
            align="left"
            label="绩效奖金">
        </el-table-column>
        <el-table-column
            prop="attendanceDeduction"
            width="200"
            align="left"
            label="考勤扣费">
        </el-table-column>
        <el-table-column
            prop="netSalary"
            width="200"
            align="left"
            label="实发工资">
        </el-table-column>
        <el-table-column
            prop="currentMonth"
            width="200"
            align="left"
            label="当前月份">
        </el-table-column>

        <!--          <el-table-column-->
        <!--              width="100"-->
        <!--              align="left"-->
        <!--              label="操作类型">-->
        <!--            <template slot-scope="scope">-->
        <!--              <el-tag  type="success" v-if="scope.row.type==0">增</el-tag>-->
        <!--              <el-tag  type="danger" v-else-if="scope.row.type==1">删</el-tag>-->
        <!--              <el-tag  type="primary" v-else-if="scope.row.type==2">改</el-tag>-->
        <!--            </template>-->
        <!--          </el-table-column>-->

      </el-table>
    </div>
</template>

<script>
    export default {
        name: "SalTable"
    }
</script>

<style scoped>

</style>