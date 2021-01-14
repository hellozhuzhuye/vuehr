<template>
  <div>
    <el-button style="margin-top: 10px" type="primary" @click="dialogVisible = true"><i
        class="el-icon-upload el-icon--right"></i> 上传文件
    </el-button>
    <el-button style="margin-top: 10px" type="primary" @click="newFolder"><i class="el-icon-folder-opened"></i> 新建文件夹
    </el-button>
    <el-button style="margin-top: 10px" type="primary" @click="initObjectList"><i class="el-icon-refresh"></i> 刷新
    </el-button>
    <el-breadcrumb style="margin-bottom: 10px;margin-top: 20px" separator="/">
      <el-breadcrumb-item>
        <el-link @click="clearGlobalObjectName">/</el-link>
      </el-breadcrumb-item>
      <el-breadcrumb-item :key="index" v-for="(item, index) in globalObjectNameArray">
        <el-link @click="breadClick(index,item)">
          {{ item }}
        </el-link>
      </el-breadcrumb-item>
    </el-breadcrumb>
    <el-table
        :data="tableData"
        tooltip-effect="dark"
        style="width: 100%"
        @selection-change="handleSelectionChange">
      <el-table-column
          type="selection"
          width="55">
      </el-table-column>
      <el-table-column
          width="60">
        <template slot-scope="scope">
          <svg v-if="scope.row.isFolder==1" class="icon" aria-hidden="true">
            <use xlink:href="#el-icon-aliwenjianjia"></use>
          </svg>
          <svg v-if="scope.row.isFolder==0" class="icon" aria-hidden="true">
            <use :href="mimeTypeMap[scope.row.iconCode]"></use>
          </svg>

        </template>

      </el-table-column>

      <el-table-column
          label="文件名"
          width="250">
        <template slot-scope="scope">
          <el-link v-if="scope.row.isFolder==0">{{ scope.row.key }}
          </el-link>
          <el-link v-if="scope.row.isFolder==1" @click="objectClick(scope.row)" :disabled="scope.row.isFolder==0"
                   type="primary">{{ scope.row.key }}
          </el-link>
        </template>
      </el-table-column>
      <el-table-column
          prop="size"
          label="大小"
          width="120">
      </el-table-column>
      <el-table-column
          prop="lastModified"
          label="最后修改时间"
          show-overflow-tooltip>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button v-if="scope.row.isFolder==0"
                     type="primary"
                     size="mini"
                     @click="handleDownload(scope.row)">
            下载
          </el-button>
          <el-button
              v-if="scope.row.isFolder==0"
              size="mini"
              @click="handleShare(scope.row)">分享
          </el-button>
          <el-button
              size="mini"
              @click="handleRename(scope.row)">重命名
          </el-button>
          <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.row)">删除
          </el-button>

        </template>
      </el-table-column>
    </el-table>
    <el-dialog
        title="文件上传"
        :visible.sync="dialogVisible"
        width="50%">

      <el-upload
          class="upload-demo"
          drag
          show-file-list
          multiple
          :on-success="onSuccess"
          :on-error="onError"
          :before-upload="beforeUpload"
          action="http://oss.smartsoftware.top"
          :data="dataObj"
          :file-list="fileList">
        <i class="el-icon-upload"></i>
        <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
        <div class="el-upload__tip" slot="tip">只能上传不超过500mb的文件</div>
      </el-upload>
      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogOk">确 定</el-button>
    </span>
    </el-dialog>

    <el-dialog
        title="分享"
        :visible.sync="shareDialogVisible"
        width="30%">
      <span>为防止机密文件泄露，请设置下载链接到期时间，以小时为单位</span>
      <div>
        <el-input-number v-model="expirationHours" :min="1" :max="10" label="链接到期时间"></el-input-number>
        <el-tag>小时</el-tag>
      </div>
      <el-input
          :readonly="true"
          type="textarea"
          autosize
          placeholder="生成的下载链接"
          v-model="shareUrl">
      </el-input>
      <div id="qrcode" ref="qrcode"></div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="makeShareUrl">生成链接</el-button>
      <el-button type="primary" @click="makeQrcode">生成二维码</el-button>
      <el-button @click="shareDialogVisible = false">取 消</el-button>
      </span>
    </el-dialog>


  </div>
</template>


<script>
import QRCode from 'qrcodejs2'

export default {
  name: "Oss",
  data() {
    return {
      shareUrl: '',
      expirationHours: 1,
      mimeTypeMap: {
        0: '#el-icon-aliexe1',
        1: '#el-icon-alitxt',
        2: '#el-icon-aliXML',
        3: '#el-icon-alijavascript-map',
        4: '#el-icon-aliHTML',
        5: '#el-icon-aliCSS',
        6: '#el-icon-aliwendang',
        21: '#el-icon-alitupian',
        31: '#el-icon-alishipin',
        41: '#el-icon-aliyinle',
        51: '#el-icon-aliswf',
        61: '#el-icon-aliMicrosoft-Excel',
        62: '#el-icon-aliWORD',
        63: '#el-icon-aliPPT',
        64: '#el-icon-aliPDF',
        65: '#el-icon-aliyasuobao',
        66: '#el-icon-aliexe'
      },
      shareDialogVisible: false,
      downloadUrl: '',
      globalObjectPath: 'cloud/',
      globalObjectNameArray: [],
      fileList: [],
      dataObj: {
        policy: '',
        signature: '',
        key: '',
        ossaccessKeyId: '',
        dir: '',
        host: ''
      },
      tableData: [],
      dialogVisible: false,
      shareObjectName: ''
    };
  },
  mounted() {
    this.initObjectList()
  },
  methods: {
    newFolder() {
      this.$prompt('请输入文件夹名称，以/字符结尾。', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
      }).then(({value}) => {
        this.getRequest("/cloud/oss/newFolder", {"newFolderName": this.globalObjectPath + value})
            .then(resp => {
              if (resp) {
                this.$message({
                  type: 'success',
                  message: '新建文件夹' + value + '成功'
                })
              }
            })
        this.initObjectList();
      })
    },
    handleRename(row) {
      this.$prompt('请输入新命名，包括后缀名，不能以 / 或者 \\ 字符开头。', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
      }).then(({value}) => {
        this.getRequest("/cloud/oss/renameObject", {
          "sourceObjectName": this.globalObjectPath + row.key,
          "destinationObjectName": this.globalObjectPath + value
        })
            .then(resp => {
              if (resp) {
                this.$message({
                      type: 'success',
                      message: '成功重命名为: ' + value
                    }
                );
                this.initObjectList();
              }
            })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '取消输入'
        });
      });
    },
    handleDelete(row) {
      var messageStr = '';
      if (row.isFolder == 1) {
        messageStr = '此操作将永久删除【' + row.key + '】文件夹下的所有文件, 是否继续?';
      } else {
        messageStr = '此操作将永久删除【' + row.key + '】, 是否继续?';
      }
      this.$confirm(messageStr, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.getRequest("/cloud/oss/deleteObject", {"delObject": this.globalObjectPath + row.key}).then(resp => {
          if (resp) {
            this.initObjectList();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    handleSelectionChange() {

    },
    clearGlobalObjectName() {
      this.globalObjectNameArray = [];
      this.globalObjectPath = 'cloud/';
      this.initObjectList();
    },
    breadClick(index) {
      let arrLength = this.globalObjectNameArray.length;
      this.globalObjectNameArray.splice(index + 1, arrLength - 1);
      this.globalObjectPath = 'cloud/';
      for (let index in this.globalObjectNameArray) {
        this.globalObjectPath += this.globalObjectNameArray[index] + '/';
      }
      this.initObjectList();
    },
    objectClick(row) {
      this.globalObjectNameArray.push(row.key.substring(0, row.key.length - 1))
      this.globalObjectPath += row.key;
      this.initObjectList();
    },
    handleShare(row) {
      this.shareUrl = '';
      if (this.qrcode != null) {
        document.getElementById('qrcode').innerHTML = '';
      }
      this.shareObjectName = row.key;
      this.shareDialogVisible = true;
    },
    async makeShareUrl() {
      var res = await this.getSignUrl(this.shareObjectName, this.expirationHours);
      this.shareUrl=res.obj;
    },
    makeQrcode() {
      if (this.qrcode != null) {
        document.getElementById('qrcode').innerHTML = '';
      }
      if (this.shareUrl == '') {
        this.$message({
          message: '请先生成下载链接',
          type: 'info'
        });
      } else {
        this.qrcode = new QRCode('qrcode', {
          text: this.shareUrl,
          width: 150,
          height: 150,
        });
      }

    },
    async handleDownload(row) {

      var res = await this.getSignUrl(this.globalObjectPath + row.key, 8);
      this.downloadUrl = res.obj;
      console.log(this.downloadUrl);

      window.open(this.downloadUrl);

      this.downloadUrl = '';
      this.$message({
        message: '请求下载成功，正在下载中...',
        type: 'success'
      });

    },
    getSignUrl: function (objectName, expirationHours) {
      return new Promise((resolve, reject) => {
        this.getRequest("/cloud/oss/signUrl", {
          "objectName": objectName,
          "expirationHours": expirationHours
        }).then((response) => {
          resolve(response);
        }).catch((error) => {
          reject(error);
        });
      })
    },
    initObjectList() {
      this.getRequest("/cloud/oss/getObjectList", {objectName: this.globalObjectPath}).then(resp => {
        if (resp) {
          this.tableData = resp.obj;
        }
      })
    },
    onSuccess(response, file, fileList) {

      this.$message({
        message: file.name + '上传成功',
        type: 'success'
      });
      this.initObjectList();
    },
    onError(err, file) {
      this.$message({
        message: file.name + '上传失败,' + err,
        type: 'error'
      });
    },
    dialogOk() {
      this.dialogVisible = false;
      this.fileList = [];
    },
    beforeUpload() {
      let _self = this;
      return new Promise((resolve, reject) => {
        console.log(this.globalObjectPath);
        this.getRequest("/cloud/oss/policy", {dir: this.globalObjectPath}).then(resp => {
          console.log(resp);
          _self.dataObj.policy = resp.obj.policy;
          _self.dataObj.signature = resp.obj.signature;
          _self.dataObj.ossaccessKeyId = resp.obj.accessKeyId;
          if (this.globalObjectPath == 'cloud/') {
            _self.dataObj.key = '${filename}';
          } else {
            _self.dataObj.key = resp.obj.dir + '/${filename}';
          }
          _self.dataObj.dir = resp.obj.dir;
          _self.dataObj.host = resp.obj.host;
          resolve(true)
        }).catch(err => {
          console.log(err);
          reject(false);
        })
      })
    }
  },
}
</script>


<style type="text/css">
.icon {
  width: 2em;
  height: 2em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>