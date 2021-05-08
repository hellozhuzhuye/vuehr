<template>
    <div>
      <div>
        <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                  clearable
                  @clear="initLogList"
                  style="width: 350px;margin-right: 10px" v-model="ename"
                  @keydown.enter.native="initLogList" ></el-input>
        <el-button icon="el-icon-search" type="primary" @click="initLogList" >
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
              prop="name"
              align="left"
              label="姓名"
              width="100">
          </el-table-column>
          <el-table-column
              prop="username"
              width="120"
              align="left"
              label="登录用户名">
          </el-table-column>
          <el-table-column
              prop="operate"
              width="200"
              align="left"
              label="操作内容">
          </el-table-column>
          <el-table-column
              prop="addDate"
              width="200"
              align="left"
              label="操作日期">
          </el-table-column>
          <el-table-column
              width="100"
              align="left"
              label="操作类型">
            <template slot-scope="scope">
              <el-tag  type="success" v-if="scope.row.type==0">增</el-tag>
              <el-tag  type="danger" v-else-if="scope.row.type==1">删</el-tag>
              <el-tag  type="primary" v-else-if="scope.row.type==2">改</el-tag>
            </template>
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
        name: "SysLog",
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
          this.initLogList();
        },
        currentChange(currentPage) {
          this.page = currentPage;
          this.initLogList();
        },
        initLogList(){
          let url='/system/log/?pageNum=' + this.page + '&pageSize=' + this.size;
          if (this.ename){
            url += "&name=" + this.ename;
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
        this.initLogList();
      }
    }
</script>

<style scoped>

</style>