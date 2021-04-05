<template>
    <div>
      <el-table
          :data="list"
          :stripe="true"
          border
          fit
          highlight-current-row
          style="width: 100%"
      >
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
            prop="idCardNumber"
            label="身份证号"
            width="120">
        </el-table-column>
        <el-table-column align="center" label="身份证缩略图" width="150">
          <template slot-scope="scope">
            <img slot="reference" :src="scope.row.imageUrl" class="image" />
          </template>
        </el-table-column>

        <el-table-column
            prop="bankCardNumber"
            label="银行卡号"
            width="120">
        </el-table-column>
        <el-table-column align="center" label="银行卡缩略图" width="150">
          <template slot-scope="scope">
            <img slot="reference" :src="scope.row.bankCardUrl" class="image" />
          </template>
        </el-table-column>






      </el-table>
    </div>
</template>

<script>
    export default {
        name: "EmpAdv",
      data() {
        return {
          msg: 'Welcome to Your Vue.js App',
          list:[
              {id:'1',workID:'00000079',name:'王銘宇',imageUrl:"https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/wmy.png",idCardNumber:'510124198809071234',bankCardNumber:'623063030000530675',bankCardUrl:'https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/IMG_6202.JPG',data:''}
          ,{id:'2',workID:'00000080',name:'支小宝',imageUrl:"https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/1.png",idCardNumber:'123456202001011234',bankCardNumber:'5236681850000742542',bankCardUrl:'https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/IMG_6203.JPG',data:''}]
        }
      },
      mounted() {
        this.drawEmpEval();
        this.drawEmpApply();
        this.drawEmpSalary();
        this.drawEmpEdu();

      },
      methods: {

      }
    }
</script>


<style type="text/css" scoped>
.image{
  width:130px;
  height:80px;
}
</style>