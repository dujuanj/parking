<template>
  <div class="repair-detail">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/repair-management' }">故障报修管理 </el-breadcrumb-item>
      <el-breadcrumb-item>故障报修管理详情</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>
    <div>
      <div>
        <el-row>
              <el-button size="mini" v-bind:id="detailDatas.id" v-bind:types="detailDatas.type" @click="handleEdit($event)" v-if='detailDatas.status==2'>处理</el-button>
        </el-row>
        <!-- 详细信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">报修信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">故障类型:</label>
              <span>{{detailDatas.type==1?"订单异常":detailDatas.type==2?"升锁异常":detailDatas.type==3?"降锁异常":''}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">停车场:</label>
             <span>{{detailDatas.parkingLotName}}</span>
            </div>
          
            <div class="form-inline">
              <label for="totalNum" class="control-label">车位:</label>
              <span>{{detailDatas.parkingNumber}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">订单编号:</label>
              <span>{{detailDatas.orderId}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">报修人手机号:</label>
              <span>{{detailDatas.userPhone}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">故障描述:</label>
              <span>{{detailDatas.description}}</span>
            </div>
             <div class="form-inline">
              <label for="totalNum" class="control-label">报修时间:</label>
              <span>{{detailDatas.updateTime| formatTime('YMDHMS')}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">状态:</label>
              <span>{{detailDatas.status==1?"已处理":detailDatas.status==2?"待处理":''}}</span>
            </div>
             <div class="form-inline">
              <label for="totalNum" class="control-label">离场时间:</label>
              <span v-if='detailDatas.orderEndTime==null'>--</span>
              <span v-else>{{detailDatas.orderEndTime | formatTime('YMDHMS')}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">处理结果:</label>
              <span>{{detailDatas.result}}</span>
            </div>
          </div>
        </div>
      
        <!-- 处理信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">处理信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">处理人:</label>
              <span>{{detailDatas.sysName}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">账号:</label>
              <span>{{detailDatas.loginName}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">操作时间:</label>
              <span>{{detailDatas.updateTime | formatTime('YMDHMS')}}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
   
  <!-- 处理弹出框 -->
    <el-dialog title="处理故障报修" :visible.sync="dialogFormeditVisible" width="35%" id="edit">
      <el-form :model="editForm">
         <el-form-item label="请选择离场时间:" :label-width="formLabelWidth" style="color:#000" >
            <el-date-picker
      v-model="endTime"
      type="datetime"
      placeholder="选择日期时间"
      align="right"
      value-format='timestamp'
      :picker-options="pickerOptions1">
    </el-date-picker>
        </el-form-item>
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
     <!-- 处理弹框升锁降锁 -->
    <el-dialog title="处理故障报修" :visible.sync="dialogFormeditVisible1" width="35%" id="edit">
      <el-form :model="editForm">
        
       <el-form-item label="填写:" :label-width="formLabelWidth" style="color:#000" >
         <textarea name="" id="" cols="55" rows="3" v-model="result"></textarea>
        </el-form-item>
      </el-form>
      <!-- 提交按扭 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormeditVisible1 = false">取 消</el-button>
        <el-button type="primary" @click="saveEdit2()">确 定</el-button>
      </div>
    </el-dialog>
  
  </div>
</template>
<script>

// import gateway from "./banner-management.vue";
export default {
  name: "repairDetail",
  data() {
    return {
      dialogFormeditVisible: false,
      dialogFormeditVisible1:false,
      formLabelWidth: "115px",
      editTime:'',
    
      dialogVisible: false,
      dialogBind: false, //绑定弹出框
      parkingLotId:'',//停车场id
      detailId: "", //详情ID,
      detailDatas: {}, //详情数据
       //查询所有停车场
      queryParkingLotdata: "",
      types:'',
      id:'',
      result:'',
      endTime:''
     
    };
  },
  methods: {
    //详情数据
    getDetailData() {
  
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/order/repairDetail",
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
    
    //处理弹框
    handleEdit(event) {
      this.types=$(event.currentTarget).attr("types");
      if(this.types==1){
           this.dialogFormeditVisible = true;
      }else{
         this.dialogFormeditVisible1 = true;
      }
     
      this.id= $(event.currentTarget).attr("id");
    },
      //处理确定
    saveEdit(){
        
         this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/order/repairDo",
          {
            id:this.id,
            endTime:this.endTime,
            result:this.result,
             repairUserId:sessionStorage.getItem("managerId")
            // createUser:sessionStorage.getItem("username")
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
            message: res.data.dataArray,
           
          });
          this.handleUserList(this.currentPage);
          this.result='';
        })
        .catch(res => {
          console.log("err");
        });
    },
        //升锁降锁处理
     saveEdit2(){
        
         this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/order/repairDo",
          {
            id:this.id,
            result:this.result,
             repairUserId:sessionStorage.getItem("managerId")
            // endTime:this.endTime,
            // createUser:sessionStorage.getItem("username")
            },
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data);
          this.dialogFormeditVisible1=false;
           this.$message({
            type: "success",
            message: res.data.dataArray,
           
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

