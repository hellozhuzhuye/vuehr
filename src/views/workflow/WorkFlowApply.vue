<template>
  <div>

    <div style=" margin-top: 10px;
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;">

      <el-card>
        <div class="img-container">
          <img src="../../assets/workflow/baoxiao.jpg" class="image">
        </div>
        <div style="margin-top: 20px; display: flex; justify-content: center;">
          <el-button @click="clickHandler(1)" type="text" class="button" size="medium">报销申请</el-button>
          <el-button type="text" class="button" size="medium">模板下载</el-button>
        </div>
      </el-card>

      <el-card>
        <div class="img-container">
          <img src="../../assets/workflow/chuchai.jpg" class="image">
        </div>
        <div style="margin-top: 20px; display: flex; justify-content: center;">
          <el-button @click="clickHandler(2)" type="text" class="button" size="medium">出差申请</el-button>
          <el-button type="text" class="button" size="medium">模板下载</el-button>
        </div>
      </el-card>

      <el-card>
        <div class="img-container">
          <img src="../../assets/workflow/qingjia.jpg" class="image">
        </div>
        <div style="margin-top: 20px; display: flex; justify-content: center;">
          <el-button @click="clickHandler(3)" type="text" class="button" size="medium">请假申请</el-button>
          <el-button type="text" class="button" size="medium">模板下载</el-button>
        </div>
      </el-card>
    </div>


    <div style=" margin-top: 30px;
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;">

      <el-card>
        <div class="img-container">
          <img src="../../assets/workflow/jiaban.jpg" class="image">
        </div>
        <div style="margin-top: 20px; display: flex; justify-content: center;">
          <el-button @click="clickHandler(4)" type="text" class="button" size="medium">加班申请</el-button>
          <el-button type="text" class="button" size="medium">模板下载</el-button>
        </div>
      </el-card>

      <el-card>
        <div class="img-container">
          <img src="../../assets/workflow/zhuanzheng.jpg" class="image">
        </div>
        <div style="margin-top: 20px; display: flex; justify-content: center;">
          <el-button @click="clickHandler(5)" type="text" class="button" size="medium">转正申请</el-button>
          <el-button type="text" class="button" size="medium">模板下载</el-button>
        </div>
      </el-card>

      <el-card>
        <div class="img-container">
          <img src="../../assets/workflow/lizhi.jpg" class="image">
        </div>
        <div style="margin-top: 20px; display: flex; justify-content: center;">
          <el-button @click="clickHandler(6)" type="text" class="button" size="medium">离职申请</el-button>
          <el-button type="text" class="button" size="medium">模板下载</el-button>
        </div>
      </el-card>
    </div>

    <el-dialog
        title="流程申请"
        :visible.sync="dialogVisible"
        width="30%">

      <el-input
          style="margin-top: 10px"
          type="textarea"
          :rows="2"
          autosize
          placeholder="请输入申请附注"
          v-model="textarea">
      </el-input>

      <el-date-picker
          style="margin-top: 10px "
          v-model="pickDate"
          align="right"
          type="date"
          placeholder="请选择日期"
          :picker-options="pickerOptions">
      </el-date-picker>

      <el-input placeholder="请选择审核人" v-model="hrNames" style="width: 300px;margin-top: 10px">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllHrs"></el-button>
      </el-input>

      <div style="margin-top: 10px ;display: flex; justify-content: left;">
        <el-upload
            class="upload-demo"
            drag
            action="https://jsonplaceholder.typicode.com/posts/"
            multiple>
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">请上传填写完毕的申请文档</div>
        </el-upload>
      </div>

      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogCancel">取 消</el-button>
    <el-button type="primary" @click="addDialogOk">确 定</el-button>
      </span>
    </el-dialog>

    <el-dialog
        title="流程申请"
        :visible.sync="advDialogVisible"
        width="30%">

      <el-input
          style="margin-top: 10px"
          type="textarea"
          :rows="2"
          autosize
          placeholder="请输入申请附注"
          v-model="textarea">
      </el-input>

      <div class="block" style="margin-top: 10px">
        <el-date-picker
            v-model="dateTime"
            type="datetimerange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期">
        </el-date-picker>
      </div>

      <el-input placeholder="请选择审核人" v-model="hrids" style="width: 300px;margin-top: 10px">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllHrs"></el-button>
      </el-input>

      <div style="margin-top: 10px ;display: flex; justify-content: left;">
        <el-upload
            class="upload-demo"
            drag
            action="https://jsonplaceholder.typicode.com/posts/"
            multiple>
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">请上传填写完毕的申请文档</div>
        </el-upload>
      </div>


      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogCancel">取 消</el-button>
    <el-button type="primary" @click="advAddDialogOk">确 定</el-button>
      </span>
    </el-dialog>

    <el-dialog
        title="选择审核人"
        :visible.sync="selectHrsVisible"
        width="40%">
      <div>
        <el-input placeholder="请输入姓名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                  clearable
                  @clear="getAllHrs"
                  style="width: 350px;margin-right: 10px" v-model="name"
                  @keydown.enter.native="getAllHrs"></el-input>
        <el-button icon="el-icon-search" type="primary" @click="getAllHrs">
          搜索
        </el-button>
      </div>
      <el-table
          :data="hrs"
          highlight-current-row
          @selection-change="handleSelectionChange"
          border
          v-loading="loading"
          element-loading-text="正在加载..."
          element-loading-spinner="el-icon-loading"
          element-loading-background="rgba(0, 0, 0, 0.8)"
          size="small"
          style="width: 80%;margin-top: 10px">
        <el-table-column
            type="selection"
            width="55">
        </el-table-column>
        <el-table-column
            prop="id"
            label="编号"
            width="75">
        </el-table-column>
        <el-table-column
            prop="name"
            label="姓名"
            width="100">
        </el-table-column>
        <el-table-column
            prop="roles[0].nameZh"
            label="职位"
            width="200">
        </el-table-column>
        <el-table-column
            prop="phone"
            label="电话"
            width="150">
        </el-table-column>
      </el-table>
      <span slot="footer" class="dialog-footer">
        <el-button @click="selectHrsCancel">取 消</el-button>
        <el-button type="primary" @click="selectHrs">确 定</el-button>
        </span>
    </el-dialog>


  </div>

</template>

<script>
export default {
  name: "WorkFlowApply",
  data() {
    return {
      multipleSelection:[],
      name:'',
      loading:false,
      addWorkFlowApply:{
        workFlowTypeId:1,
        hrid:"10,11,12",
        content:"",
        selectDateTime:"",
        url:"http://smartsoftware.top"
      },
      selectHrsVisible:false,
      hrids:[],
      hrNames:[],
      hrs:[],
      clickType: 0,
      dialogVisible: false,
      textarea: '',
      dateTime: [],
      pickDate: '',
      advDialogVisible: false,
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
  methods: {
    selectHrsCancel(){
      this.hrids=[];
      this.hrNames=[];
      this.selectHrsVisible=false;
    },
    selectHrs(){
      this.hrids=[];
      this.hrNames=[];
      this.multipleSelection.forEach(item => {
        this.hrids.push(item.id);
        this.hrNames.push(item.name);
      });
      this.selectHrsVisible=false;
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    addDialogCancel(){
      this.hrids=[];
      this.hrNames=[];
      this.multipleSelection=[];
    },
    addDialogOk(){
      if (this.textarea && this.pickDate) {
        this.addWorkFlowApply.content=this.textarea;
        this.addWorkFlowApply.hrid=this.hrids.toString();
        this.addWorkFlowApply.selectDateTime=this.pickDate;

        this.postRequest("/workflow/apply/", this.addWorkFlowApply).then(resp => {
          if (resp) {
            this.addWorkFlowApply = {
              workFlowTypeId:1,
              hrid:"",
              content:"",
              selectDateTime:"",
              url:"http://smartsoftware.top"
            };
            this.dialogVisible = false;
          }
        });
      } else {
        this.$message.error("字段不能为空");
      }
    },
    advAddDialogOk(){
      if (this.textarea && this.dateTime) {
        this.addWorkFlowApply.content=this.textarea;
        // this.addWorkFlowApply.hrid=this.hrids;
        this.addWorkFlowApply.selectDateTime=this.dateTime.toString();

        this.postRequest("/workflow/apply/", this.addWorkFlowApply).then(resp => {
          if (resp) {
            this.addWorkFlowApply = {
              workFlowTypeId:1,
              hrid:"10,11,12",
              content:"",
              selectDateTime:"",
              url:"http://smartsoftware.top"
            };
            this.dialogVisible = false;
          }
        });
      } else {
        this.$message.error("字段不能为空");
      }
    },
    getAllHrs() {
      this.selectHrsVisible = true;
      let url = '/system/hr/';
      if (this.name) {
        url += "?name=" + this.name;
      }
      this.getRequest(url).then(resp => {
        this.loading = false;
        if (resp) {
          this.hrs = resp;
        }
      });
    },
    clickHandler(type) {
      if (type == 1 || type == 5 || type == 6) {
        this.dialogVisible = true;
      } else {
        this.advDialogVisible = true;
      }
      this.addWorkFlowApply.workFlowTypeId=type;
    },
    dialogCancel() {
      this.advDialogVisible = false;
      this.dialogVisible = false;
    }
  }
}
</script>

<style>


.bottom {
  margin-top: 13px;
  line-height: 12px;
  display: flex;
  justify-content: center;
}

.button {
  /*padding: 0;*/
  /*float: right;*/
}

.image {
  width: 200px;
  height: 200px;
  border-radius: 50px;
}


.img-container {
  width: 100%;
  display: flex;
  justify-content: center;
}
</style>