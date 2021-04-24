<template>
  <div>
    <div style="margin-top: 20px;margin-bottom: 10px">
      <el-input placeholder="请输入员工名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                clearable
                @clear="inidEmpAdv"
                style="width: 350px;margin-right: 10px" v-model="empName"
                @keydown.enter.native="inidEmpAdv"></el-input>
      <el-button icon="el-icon-search" type="primary" @click="inidEmpAdv">
        搜索
      </el-button>

      <el-button icon="el-icon-plus" type="primary" @click="ocrVisible = true">
        添加
      </el-button>
    </div>
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
          prop="eid"
          label="工号"
          width="100">
      </el-table-column>
      <el-table-column
          prop="idCardVO.name"
          label="姓名"
          width="80">
      </el-table-column>
      <el-table-column
          prop="idCardVO.num"
          label="身份证号"
          width="200">
      </el-table-column>
      <el-table-column align="center" label="身份证缩略图" width="200">
        <template slot-scope="scope">
          <img slot="reference" :src="scope.row.idCardUrl" class="image"/>
        </template>
      </el-table-column>

      <el-table-column
          prop="bankCardVO.cardNum"
          label="银行卡号"
          width="200">
      </el-table-column>
      <el-table-column align="center" label="银行卡缩略图" width="200">
        <template slot-scope="scope">
          <img slot="reference" :src="scope.row.bankCardUrl" class="image"/>
        </template>
      </el-table-column>

      <el-table-column
          width="200"
          label="操作">
        <template slot-scope="scope">
          <el-button @click="getEmpAdvDetail(scope.row.id)" type="primary">详细</el-button>
          <el-button @click="delEmpSenior(scope.row)" type="danger">删除</el-button>
        </template>
      </el-table-column>

    </el-table>

    <el-dialog
        title="详细信息"
        :visible.sync="detailVisible"
        width="30%">
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCard.name">
        <template slot="prepend">姓名</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCard.address">
        <template slot="prepend">地址</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCard.nationality">
        <template slot="prepend">名族</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCard.num">
        <template slot="prepend">身份证号</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCard.sex">
        <template slot="prepend">性别</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCard.birth">
        <template slot="prepend">出生日期</template>
      </el-input>


      <el-input style="margin-top: 30px" placeholder="请输入内容" v-model="detailBankCard.bankName">
        <template slot="prepend">银行名称</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailBankCard.cardNum">
        <template slot="prepend">银行卡号</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailBankCard.validDate">
        <template slot="prepend">有效期</template>
      </el-input>

      <span slot="footer" class="dialog-footer">
    <el-button @click="detailVisible = false">取 消</el-button>
    <el-button type="primary" @click="detailVisible = false">确 定</el-button>
  </span>
    </el-dialog>

    <el-dialog
        title="身份证识别及银行卡识别"
        :visible.sync="ocrVisible"
        width="30%">
      <el-input placeholder="请选择员工" v-model="empSelection.name"
                style="width: 300px;margin-top: 10px;margin-bottom: 20px">
        <el-button icon="el-icon-search" slot="prepend" @click="getAllEmps"></el-button>
      </el-input>
      <el-upload
          class="upload-demo"
          drag
          show-file-list
          multiple
          :on-success="idCardOnSuccess"
          :on-error="idCardOnError"
          :before-upload="idCardBeforeUpload"
          action="http://oss.smartsoftware.top"
          :data="dataObj"
          :file-list="fileList">
        <i class="el-icon-upload"></i>
        <div class="el-upload__text">将身份证图片拖到此处，或<em>点击上传</em></div>
        <div class="el-upload__tip" slot="tip" style="margin: 20px">只能上传不超过5mb的文件</div>
      </el-upload>
      <el-button type="primary" @click="idCardOcr">开始身份证识别</el-button>

      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCardOcr.name">
        <template slot="prepend">姓名</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCardOcr.address">
        <template slot="prepend">地址</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCardOcr.nationality">
        <template slot="prepend">名族</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCardOcr.num">
        <template slot="prepend">身份证号</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailIdCardOcr.sex">
        <template slot="prepend">性别</template>
      </el-input>
      <el-input style="margin-top: 10px;margin-bottom: 20px" placeholder="请输入内容" v-model="detailIdCardOcr.birth">
        <template slot="prepend">出生日期</template>
      </el-input>

      <el-upload
          class="upload-demo"
          drag
          show-file-list
          multiple
          :on-success="bankCardOnSuccess"
          :on-error="bankCardOnError"
          :before-upload="bankCardBeforeUpload"
          action="http://oss.smartsoftware.top"
          :data="bankCardDataObj"
          :file-list="bankCardFileList">
        <i class="el-icon-upload"></i>
        <div class="el-upload__text">将银行卡图片拖到此处，或<em>点击上传</em></div>
        <div class="el-upload__tip" slot="tip" style="margin: 20px">只能上传不超过5mb的文件</div>
      </el-upload>
      <el-button type="primary" @click="bankCardOcr">开始银行卡识别</el-button>

      <el-input style="margin-top: 30px" placeholder="请输入内容" v-model="detailBankCardOcr.bank_name">
        <template slot="prepend">银行名称</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailBankCardOcr.card_num">
        <template slot="prepend">银行卡号</template>
      </el-input>
      <el-input style="margin-top: 10px" placeholder="请输入内容" v-model="detailBankCardOcr.valid_date">
        <template slot="prepend">有效期</template>
      </el-input>

      <span slot="footer" class="dialog-footer">
    <el-button @click="detailVisible = false">取 消</el-button>
    <el-button type="primary" @click="insertEmpAdv">确 定</el-button>
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
    <el-button type="primary" @click="selectEmpsVisible = false">确 定</el-button>
    </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "EmpAdv",
  data() {
    return {
      loading: false,
      name: '',
      page: 1,
      size: 10,
      total: 0,
      empSelection: {},
      idCardOcrUrl: '',
      idCardObjectName: '',
      bankCardOcrUrl: '',
      bankCardObjectName: '',
      ocrVisible: false,
      fileList: [],
      dataObj: {
        policy: '',
        signature: '',
        key: '',
        ossaccessKeyId: '',
        dir: '',
        host: ''
      },
      bankCardFileList: [],
      bankCardDataObj: {
        policy: '',
        signature: '',
        key: '',
        ossaccessKeyId: '',
        dir: '',
        host: ''
      },
      empName: '',
      detailIdCard: {},
      detailBankCard: {},
      detailVisible: false,
      list: [],
      detailIdCardOcr: {},
      detailBankCardOcr: {},
      emps: [],
      selectEmpsVisible: false

      // list:[
      //     {id:'1',workID:'00000079',name:'王銘宇',imageUrl:"https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/wmy.png",idCardNumber:'510124198809071234',bankCardNumber:'623063030000530675',bankCardUrl:'https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/IMG_6202.JPG',data:''}
      // ,{id:'2',workID:'00000080',name:'支小宝',imageUrl:"https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/1.png",idCardNumber:'123456202001011234',bankCardNumber:'5236681850000742542',bankCardUrl:'https://826076402.oss-cn-shenzhen.aliyuncs.com/images/idCard/IMG_6203.JPG',data:''}]
    }
  },
  mounted() {
    this.inidEmpAdv();

  },
  methods: {
    insertEmpAdv() {
      this.postRequest("/employee/advanced/addEmpSenior",
          {
            "eid": this.empSelection.id,
            "idCardData": JSON.stringify(this.detailIdCardOcr),
            "bankCardData": JSON.stringify(this.detailBankCardOcr),
            "bankCardUrl": "http://oss.smartsoftware.top/" + this.bankCardObjectName,
            "idCardUrl": "http://oss.smartsoftware.top/" + this.idCardObjectName
          }).then(resp => {
        this.inidEmpAdv();
        this.ocrVisible = false;
      })
    },
    sizeChange(currentSize) {
      this.size = currentSize;
      this.getAllEmps();
    },
    currentChange(currentPage) {
      this.page = currentPage;
      this.getAllEmps();
    },
    handleEmpSelectionChange(val) {
      console.log(val);
      this.empSelection = val;
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
    idCardOcr() {
      this.getRequest("/cloud/oss/idCardOcrSignUrl", {
        "objectName": this.idCardObjectName,
        "expirationHours": 1
      }).then(resp => {
            console.log(resp);
            this.detailIdCardOcr = resp.obj;
          }
      );
    },
    bankCardOcr() {
      this.getRequest("/cloud/oss/bankCardOcrSignUrl", {
        "objectName": this.bankCardObjectName,
        "expirationHours": 1
      }).then(resp => {
            console.log(resp);
            this.detailBankCardOcr = resp.obj;
          }
      );
    },
    bankCardOnSuccess(response, file, fileList) {
      this.bankCardObjectName += file.name;
      console.log(this.idCardObjectName);
      this.$message({
        message: file.name + '上传成功',
        type: 'success'
      });

    },
    bankCardOnError(err, file) {
      this.$message({
        message: file.name + '上传失败,' + err,
        type: 'error'
      });
    },

    idCardOnSuccess(response, file, fileList) {
      this.idCardObjectName += file.name;
      console.log(this.idCardObjectName);
      this.$message({
        message: file.name + '上传成功',
        type: 'success'
      });

    },
    idCardOnError(err, file) {
      this.$message({
        message: file.name + '上传失败,' + err,
        type: 'error'
      });
    },
    dialogOk() {
      this.dialogVisible = false;
      this.fileList = [];
    },
    pad2: function (n) {
      return n < 10 ? '0' + n : n
    },
    idCardBeforeUpload() {
      let _self = this;
      return new Promise((resolve, reject) => {
        this.getRequest("/cloud/oss/ocrPolicy", {dir: "images/idCard"}).then(resp => {
          console.log(resp);
          _self.dataObj.policy = resp.obj.policy;
          _self.dataObj.signature = resp.obj.signature;
          _self.dataObj.ossaccessKeyId = resp.obj.accessKeyId;
          var date = new Date();
          var dateStr = date.getFullYear().toString()
              + _self.pad2(date.getMonth() + 1)
              + _self.pad2(date.getDate())
              + _self.pad2(date.getHours())
              + _self.pad2(date.getMinutes())
              + _self.pad2(date.getSeconds());
          console.log(dateStr);
          _self.dataObj.key = resp.obj.dir + '/' + dateStr + '_${filename}';
          _self.dataObj.dir = resp.obj.dir;
          this.idCardObjectName = resp.obj.dir + '/' + dateStr + '_';
          _self.dataObj.host = resp.obj.host;
          resolve(true)
        }).catch(err => {
          console.log(err);
          reject(false);
        })
      })
    },
    bankCardBeforeUpload() {
      let _self = this;
      return new Promise((resolve, reject) => {
        this.getRequest("/cloud/oss/ocrPolicy", {dir: "images/bankCard"}).then(resp => {
          console.log(resp);
          _self.bankCardDataObj.policy = resp.obj.policy;
          _self.bankCardDataObj.signature = resp.obj.signature;
          _self.bankCardDataObj.ossaccessKeyId = resp.obj.accessKeyId;
          var date = new Date();
          var dateStr = date.getFullYear().toString()
              + _self.pad2(date.getMonth() + 1)
              + _self.pad2(date.getDate())
              + _self.pad2(date.getHours())
              + _self.pad2(date.getMinutes())
              + _self.pad2(date.getSeconds());
          console.log(dateStr);
          _self.bankCardDataObj.key = resp.obj.dir + '/' + dateStr + '_${filename}';
          _self.bankCardDataObj.dir = resp.obj.dir;
          this.bankCardObjectName = resp.obj.dir + '/' + dateStr + '_';
          _self.bankCardDataObj.host = resp.obj.host;
          resolve(true)
        }).catch(err => {
          console.log(err);
          reject(false);
        })
      })
    },
    inidEmpAdv() {
      this.getRequest("/employee/advanced/getEmpSeniorByPage", {pageNum: 1, pageSize: 10}).then(resp => {
        if (resp) {
          this.list = resp.list;
        }
      })
    },
    delEmpSenior(data){

      this.$confirm('此操作将永久删除该资料, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest("/employee/advanced/" + data.id).then(resp => {
          if (resp) {
            this.inidEmpAdv();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    getEmpAdvDetail(id) {
      this.getRequest("/employee/advanced/getEmpSeniorDetail", {id: id}).then(resp => {
        if (resp) {
          console.log(resp);
          this.detailIdCard = resp.idCardVO;
          this.detailBankCard = resp.bankCardVO;
          this.detailVisible = true;
        }
      })
    }
  }
}
</script>


<style type="text/css" scoped>
.image {
  width: 130px;
  height: 80px;
}
</style>