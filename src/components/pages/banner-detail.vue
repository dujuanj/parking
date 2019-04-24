<template>
  <div class="banner-detail">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/banner-management' }">广告管理</el-breadcrumb-item>
      <el-breadcrumb-item>广告管理详情</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>
    <div>
      <div>
        <el-row>
          <el-button type="primary" @click="dialogFormeditVisible = true" size="small">编辑</el-button>
          <el-button type="primary" @click="open2" size="small">删除</el-button>
        </el-row>
        <!-- 详细信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">详细信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">应用场景:</label>
              <span>{{detailDatas.usedScene==1?"HISS停车APP":''}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">广告类型:</label>
              <span>{{detailDatas.type==1?"活动":detailDatas.type==0?"默认":''}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">广告名称:</label>
              <span>{{detailDatas.name}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">广告图片:</label>
              <!-- <span>{{detailDatas.view}}</span> -->
              <img :src="detailDatas.view" alt="" style='width:200px;height:150px;'>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">开始日期:</label>
              <span>{{detailDatas.startTime | formatTime('YMDHMS')}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">结束日期:</label>
              <span>{{detailDatas.endTime|formatTime('YMDHMS')}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">启用状态:</label>
              <span>{{detailDatas.status==1?"关闭":detailDatas.status==0?"启用":detailDatas.status==2?"已过期":''}}</span>
            </div>
             <div class="form-inline">
              <label for="totalNum" class="control-label">跳转链接:</label>
              <span>{{detailDatas.jumpUrl}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">广告描述:</label>
              <span>{{detailDatas.content}}</span>
            </div>
          </div>
        </div>
      
        <!-- 创建信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">创建信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">创建人:</label>
              <span>{{detailDatas.createUserName}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">账号:</label>
              <span>{{detailDatas.createLoginName}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">创建日期:</label>
              <span>{{detailDatas.createTime | formatTime('YMDHMS')}}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
   

   <!-- 编辑弹出框 -->
    <el-dialog title="编辑广告" :visible.sync="dialogFormeditVisible" width="35%" id="edit">
      <el-form :model="editForm">
       <el-form-item label="应用场景:" :label-width="formLabelWidth" style="color:#000" >
          <select v-model="editForm.usedScene"  class="myselect">
          <option value>选择应用场景(全部)</option>
          <!-- <option :value="item.id" v-for="item in queryParkingLotdata">{{item.name}}</option> -->
             <option value='1'>HISS停车APP</option>
          </select>
        </el-form-item>
        <el-form-item
          label="应用位置:"
          :label-width="formLabelWidth"
          style="color:#000"
       
        >
          <select v-model="editForm.name"  class="myselect">
            <option value="">选择应用位置(全部)</option>
            <option value="APP启动页">APP启动页</option>
            <option value="APP首页轮播">APP首页轮播</option>
            
          </select>
        </el-form-item>
        <el-form-item
          label="广告性质:"
          :label-width="formLabelWidth"
          style="color:#000"
        
        >
          <select v-model="editForm.type"  class="myselect">
            <option value="">选择广告性质</option>
            <option value="0">默认</option>
            <option value="1">活动</option>
            
          </select>
        </el-form-item>

        <el-form-item
          label="位置序号"
          :label-width="formLabelWidth"
          style="color:#000"
          
        >
          <el-input autocomplete="off" v-model="editForm.number"></el-input>
        </el-form-item>
        <el-form-item
          label="广告图片:"
          :label-width="formLabelWidth"
          style="color:#000"
         
        >
          <el-upload
            action
            accept="image/*"
            :multiple="true"
            list-type="picture-card"
            :on-preview="handlePictureCardPreview"
            :file-list="imgList"
            name="uploadFile"
            
            :http-request="httpRequest"
             :show-file-list=true
          >
            <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
            <img width="100%" :src="dialogImageUrl" alt>
          </el-dialog>
        </el-form-item>
        <el-form-item
          label="有效期限:"
          :label-width="formLabelWidth"
          style="color:#000"
         
        >
          <el-date-picker
            v-model="editTime"
            type="daterange"
            align="right"
            unlink-panels
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          ></el-date-picker>
        </el-form-item>
         <el-form-item
          label="启用状态:"
          :label-width="formLabelWidth"
          style="color:#000"
       
        >
          <select v-model="editForm.status"  class="myselect">
            <option value="">选择启用状态</option>
            <option value="0">启用</option>
          <option value="1">关闭</option>
          <option value="2">已过期</option>
            
          </select>
        </el-form-item>
          <el-form-item
          label="跳转链接"
          :label-width="formLabelWidth"
          style="color:#000"
        
        >
          <el-input autocomplete="off" v-model="editForm.jumpUrl"></el-input>
        </el-form-item>
         <el-form-item
          label="广告描述"
          :label-width="formLabelWidth"
          style="color:#000"
          
        >
          <el-input autocomplete="off" v-model="editForm.content"></el-input>
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

import gateway from "./banner-management.vue";
export default {
  name: "bannerDetail",
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
      //编辑数据
      editForm: {
        id: "",
        number: "",
        status: "",
        usedScene: "",
        content: "",
        startTime: "",
        endTime: "",
        jumpUrl:'',
        view:'',
      },
    };
  },
  methods: {
    //详情数据
    getDetailData() {
      this.imgList=[];
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/advertising/bannerInfo",
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
          this.editForm=res.data.dataArray;
          var obj=new Object();
          obj.url=res.data.dataArray.view;
          this.imgList.push(obj);
          this.editTime=[res.data.dataArray.startTime,res.data.dataArray.endTime]
          
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
    //删除弹出框
   open2() {
      var id = this.detailId;
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$http
          .post(
            this.GLOBAL.xgurl + "/park-api/park/advertising/deleteBanner",
            {
              id: id
            },
            {
              headers: {
                "Content-Type": "application/json;charset=UTF-8"
              }
            }
          )
          .then(res => {
            console.log(res.data);
            this.$message({
              type: "success",
              message: res.data.errorMsg
            });
           this.getDetailData();
          })
          .catch(res => {
            console.log("err");
          });
      });
    },
   
   
     //查询所有停车场
    queryParkingSpace() {
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLot/queryAllParkingLot",

          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
          this.queryParkingLotdata = res.data.dataArray;
        })
        .catch(res => {
          console.log("err");
        });
    },
   //编辑保存
    saveEdit(){
        let _this = this;
        console.log(this.editForm);
       
         this.dialogFormeditVisible=false;
          this.editForm.startTime=new Date(this.editTime[0]).getTime();
          this.editForm.endTime=new Date(this.editTime[1]).getTime();
          console.log(this.editForm);
         this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/advertising/updateBanner",
          this.editForm,
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data);
           this.$message({
            type: "success",
            message: res.data.errorMsg,
           
          });
          _this.getDetailData();
        })
        .catch(res => {
          console.log("err");
        });
    },
      //图片上传转流
    httpRequest(file) {
      //alert(999);

      console.log(file.file);

      var reader = new FileReader();
      reader.readAsDataURL(file.file);
  
      reader.onload = e => {
        var imgurlbase = e.target.result.split(",");
        imgurlbase.shift();
        imgurlbase = imgurlbase.toString();
        console.log(imgurlbase);
        this.imgAdd(imgurlbase);
      };
    },
     imgAdd(imgUrl) {
      this.imgurlbase = imgUrl;

      var a;
      $.ajax({
        type: "POST",
      url:'http://192.168.10.32:9106/file/uploadFileData',
        data: { uploadFile: imgUrl,fileType:'jpg' },
        // dataType: "json",
        async: false,
        success: function(data) {
          console.log(data);
          a = data;

          return a;
        },
        error: function(json) {
          console.log(json);
        }
      });

      console.log(a);
      
      this.editForm.view=a;
    
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

