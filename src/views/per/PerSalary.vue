<template>
    <div>
      <div>
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
    </div>
</template>

<script>
    export default {
        name: "PerSalary",
      data(){
        return {
          logData:[],
          ename: "",
          page: 1,
          size: 10,
          total: 0,
          beginDateScope: null
        }
      },
      methods:{
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