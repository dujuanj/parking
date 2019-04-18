<template>
  <div class="garage-detail">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/garage-feedback' }">车场信息反馈管理</el-breadcrumb-item>
      <el-breadcrumb-item>车场信息反馈详情</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>
    <div>
      <div>
        <el-row>
              <el-button size="mini" v-bind:id="detailId" @click="handleEdit($event)" v-if='detailDatas.status==0'>处理</el-button>
        </el-row>
        <!-- 详细信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">反馈信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">停车场:</label>
              <span>{{detailDatas.parkingLotName}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">错误类型:</label>
              <span>{{detailDatas.errorType==1?"停车场不存在":detailDatas.errorType==2?"停车场不对外开放":detailDatas.errorType==3?"空闲车位状态错误":detailDatas.errorType==4?"位置错误":detailDatas.errorType=5?"收费信息错误":detailDatas.errorType==6?"停车场名称错误":detailDatas.errorType==7?"营业时段不符":detailDatas.errorType==8?"免费时长":detailDatas.errorType==9?"其它":"--"}}</span>
            </div>
          
            <div class="form-inline">
              <label for="totalNum" class="control-label">照片凭证:</label>
              <!-- <span>{{detailDatas.view}}</span> -->
              <img :src="detailDatas.pictureOne" alt="" style='width:200px;height:150px;display:inline-block;'>
               <img :src="detailDatas.pictureTwo" alt="" style='width:200px;height:150px;display:inline-block'>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">问题描述:</label>
              <span>{{detailDatas.content}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">反馈人手机号:</label>
              <span>{{detailDatas.phone}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">反馈时间:</label>
              <span>{{detailDatas.createTime | formatTime('YMDHMS')}}</span>
            </div>
             <div class="form-inline">
              <label for="totalNum" class="control-label">状态:</label>
              <span>{{detailDatas.status==0?"未解决":detailDatas.status==1?"已处理":''}}</span>
            </div>
            <div class="form-inline" v-if='detailDatas.status==1'>
              <label for="totalNum" class="control-label">处理结果:</label>
              <span>{{detailDatas.result}}</span>
            </div>
          </div>
        </div>
      
        <!-- 处理信息 -->
        <div id="space_details" class="panel panel-default" v-if='detailDatas.status==1'>
          <div class="panel-heading">处理信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">处理人:</label>
              <span>{{detailDatas.createUser}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">账号:</label>
              <span>{{detailDatas.loginName}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">操作时间:</label>
              <span>{{detailDatas.handlerTime | formatTime('YMDHMS')}}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
   
 <!-- 处理弹出框 -->
    <el-dialog title="处理车场信息反馈" :visible.sync="dialogFormeditVisible" width="35%" id="edit">
      <el-form :model="editForm">
       <el-form-item label="填写:" :label-width="formLabelWidth" style="color:#000" >
         <textarea name="" id="" cols="55" rows="3" v-model="result"></textarea>
        </el-form-item>
      </el-form>
      <!-- 提交按扭 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormeditVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveEdit()">确 定</el-button>
      </div>
    </el-dialog>
  
  </div>
</template>
<script>

// import gateway from "./banner-management.vue";
export default {
  name: "garageDetail",
  data() {
    return {
      dialogFormeditVisible: false,
      formLabelWidth: "105px",
      imgList:[],
      editTime:'',
      //上传图片
      dialogImageUrl: "",
      dialogVisible: false,
      dialogBind: false, //绑定弹出框
      parkingLotId:'',//停车场id
      detailId: "", //详情ID,
      detailDatas: {}, //详情数据
       //查询所有停车场
      queryParkingLotdata: "",
     
    };
  },
  methods: {
    //详情数据
    getDetailData() {
      this.imgList=[];
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/infofeedback/info",
          { id: this.detailId },
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
          this.detailDatas = res.data.dataArray;          
        })
        .catch(res => {
          console.log("err");
        });
    },
     handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
      console.log(this.dialogImageUrl);
    },
    //处理
    handleEdit(event) {
      this.dialogFormeditVisible = true;
     
    },
     //处理确定
    saveEdit(){
        
         this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/infofeedback/handler",
          {
            id:this.detailId,
            result:this.result,
            createUser:sessionStorage.getItem("username")
            },
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data);
          this.dialogFormeditVisible=false;
           this.$message({
            type: "success",
            message: res.data.errorMsg,
           
          });
          this.handleUserList(this.currentPage);
          this.result='';
        })
        .catch(res => {
          console.log("err");
        });
    },

  },
  created() {
     this.detailId=sessionStorage.getItem('id');
    this.detailId = this.$route.params.id;
    this.getDetailData();
    this.queryParkingSpace();
    this.findAllServer();
  }
};
</script>
<style scoped>
.el-row {
  text-align: left;
  margin-left: 18px;
  margin-top: 15px;
}
.el-pagination {
  text-align: right;
  margin-top: 15px;
  margin-right: 36px;
}
.el-table .success-row {
  background: #f0f9eb;
}
.el-button + .el-button {
  margin-left: 0 !important;
}
.el-form-item {
  margin-bottom: 10px !important;
}
.el-dialog__wrapper {
  text-align: left !important;
}
.el-input-group {
  width: 19% !important;
}
.stat-container {
  display: table;
  margin-bottom: 1.5em;
  width: 100%;
  margin-top: 30px;
}
.stat-holder {
  display: table-cell;
  width: 25%;
}
.stat {
  height: 50px;
  padding: 15px;
  margin-right: 1em;
  font-size: 12px;
  font-style: italic;
  text-align: center;
  color: #888;
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-shadow: 1px 1px 2px rgba(255, 255, 255, 0.5);
}
.stat:hover {
  cursor: pointer;
  color: #909399;
}
.stat span {
  display: block;
  margin-bottom: 0.4em;
  font-size: 26px;
  font-weight: 600;
  font-style: normal;
  color: #4a515b;
}
.index_img img {
  width: 95%;
  margin-top: 42px;
  height: 430px;
}

.num_style[data-v-1ac62fda] {
  color: #fff;
  font-size: 23px;
  border-bottom: 1px solid #fff;
  width: 200px;
  display: inline-block;
  text-align: center;
  margin-top: 33%;
}
.posi {
  position: absolute;
  top: 17%;
  left: 39%;
  color: #fff;
  font-size: 36px;
}
.count {
  width: 200px;
  height: 120px;
  background: #409eff;
  float: left;
  position: relative;
  margin-right: 15px;
  margin-left: 6%;
  margin-top: 2%;
  border-radius: 3px;
}
.home {
  font-size: 28px;
}
.rightWrap {
  margin-left: 20px !important;
}
.el-input {
  float: left;
  width: 80%;
}
.el-form-item__label {
  width: 100px;
}
</style>

