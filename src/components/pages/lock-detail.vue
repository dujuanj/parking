<template>
  <div class="lock-detail">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/ground-lock' }">地锁管理</el-breadcrumb-item>
      <el-breadcrumb-item>地锁管理详情</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>
    <div>
      <div>
        <el-row>
          <el-button type="primary" @click="dialogFormeditVisible = true" size="small">编辑</el-button>
          <el-button type="primary" @click="open2" size="small">删除</el-button>
        </el-row>
        <!-- 网关信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">地锁信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">地锁名称:</label>
              <span>{{detailDatas.name}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">地锁编号:</label>
              <span>{{detailDatas.devEui}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">所属组:</label>
              <span>{{detailDatas.appEui}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">AppKey:</label>
              <span>{{detailDatas.appKey}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">验证码:</label>
              <span>{{detailDatas.authCode}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">CLASSC模式:</label>
              <span>{{detailDatas.classc==true?"是":detailDatas.classc==false?"不是":''}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">忽略重复包:</label>
              <span>{{detailDatas.relaxfcnt}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">ADR间隔:</label>
              <span>{{detailDatas.adrInterval}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">ADR补充:</label>
              <span>{{detailDatas.installationMargin}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">经度:</label>
              <span>{{detailDatas.latitude}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">Rx1DR:</label>
              <span>{{detailDatas.rx1dr}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">Rx2DR:</label>
              <span>{{detailDatas.rx2dr}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">RxDelay:</label>
              <span>{{detailDatas.rxdelay}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">RxWindow:</label>
              <span>{{detailDatas.rxwindows}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">节点描述:</label>
              <span>{{detailDatas.description}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">网关连接:</label>
              <span>{{}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">通讯地址:</label>
              <span>{{detailDatas.devAddress}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">车锁状态:</label>
              <span>{{detailDatas.status}}</span>
            </div>
              <div class="form-inline">
              <label for="totalNum" class="control-label">网络状态:</label>
              <span>{{detailDatas.isOnline==1?"在线":detailDatas.isOnline==0?"离线":''}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">电量状态:</label>
              <span>{{detailDatas.power}}</span>
            </div>
          </div>
        </div>
        <!-- 绑定信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">绑定信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">绑定状态:</label>
              <span>{{detailDatas.isBindParkingSpace==1?"已绑定":detailDatas.isBindParkingSpace==0?"未绑定":''}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">绑定的停车场:</label>
              <span>{{detailDatas.parkingLotName}}</span>
              <label for="area" class="control-label">绑定的车位:</label>
              <span>{{detailDatas.parkingSpaceNumber}}</span>
              <span v-if="detailDatas.isBindParkingSpace==1">
                <a
                  href="javascript:;"
                  style="margin-left:20px;"
                  @click="unbind($event)"
                  :id="detailDatas.id"
                  :parkingLotId="detailDatas.parkingLotId"
                  :parkingLockId='detailDatas.parkingLockId'
                >解绑</a>
              </span>
              <span v-else>
                <a
                  href="javascript:;"
                  style="margin-left:20px;"
                  :id="detailDatas.id"
                  :parkingLotId="detailDatas.parkingLotId"
                   :parkingLockId='detailDatas.parkingLockId'
                  @click="bind($event)"
                >绑定</a>
              </span>
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
    <!-- 绑定弹出框 -->
    <el-dialog title="绑定停车场" :visible.sync="dialogBind" width="30%" >
       <select  class="myselect" v-model="parkingLotId" @change="getLot($event)">
            <option value=''>选择所属停车场(全部)</option>
            <option :value="item.id" v-for="item in queryParkingLotdata">{{item.name}}</option>
          </select>
           <select class="myselect" v-model="parkingLockId" >
          <option value=''>选择要绑定的车位</option>
          <option :value="item.parkingSpaceId" v-for="item in positions">{{item.parkingSpaceName}}</option>
        </select>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogBind = false">取 消</el-button>
        <el-button type="primary" @click='bindsure'>确 定</el-button>
      </span>
    </el-dialog>

     <!-- 编辑弹出框 -->
    <el-dialog title="编辑地锁" :visible.sync="dialogFormeditVisible" width="35%" id="edit">
      <p style="width:100%;color: #67c23a;margin-top: -25px;margin-bottom: 17px">编辑地锁信息>>></p>
       <el-form :model="editForm">
        <el-form-item label="地锁名称:" :label-width="formLabelWidth" style="color:#000" prop="name">
          <el-input autocomplete="off" v-model="editForm.name"></el-input>
        </el-form-item>
        <el-form-item
          label="地锁编号:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="gatewayMac"
        >
          <el-input autocomplete="off" v-model="editForm.devEui"></el-input>
        </el-form-item>
        <el-form-item
          label="所属组:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="serverId"
        >
          <select v-model="editForm.groupId"  class="myselect">
            <option value="">选择所属组</option>
            <option :value="item.id" v-for='item in groups'>{{item.name}}</option>
          </select>
        </el-form-item>

        <el-form-item
          label="AppKey"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="license"
        >
          <el-input autocomplete="off" v-model="editForm.appKey"></el-input>
        </el-form-item>

      

        <el-form-item
          label="验证码:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.authCode"></el-input>
        </el-form-item>
        <el-form-item
          label="CLASSC模式:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
          <select @change="handleUserList(1)" class="myselect" v-model="editForm.isClassc">
            <option value>选择是否为*CLASSC模式</option>
            <option value="true">是</option>
            <option value="false">否</option>
          </select>
        </el-form-item>
        <el-form-item
          label="忽略重复包:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
          <select @change="handleUserList(1)" class="myselect" v-model="editForm.relaxFcnt">
            
            <option value="true">是</option>
            <option value="false">否</option>
          </select>
        </el-form-item>
          <el-form-item
          label="ADR间隔:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.adrInterval"></el-input>
        </el-form-item>
         <el-form-item
          label="ADR补充:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.installationMargin"></el-input>
        </el-form-item>
          <el-form-item
          label="经度:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.latitude"></el-input>
        </el-form-item>
         <el-form-item
          label="纬度:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.longitude"></el-input>
        </el-form-item>
        <el-form-item
          label="rx1dr:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.rx1dr"></el-input>
        </el-form-item>
         <el-form-item
          label="Rx2DR:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.rx2dr"></el-input>
        </el-form-item>
         <el-form-item
          label="RxDelay:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.rxdelay"></el-input>
        </el-form-item>
          <el-form-item
          label="RxWindow:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
          <select  class="myselect" v-model="editForm.rxwindows">
            <option value=''>选择RxWindow</option>
            <option value="rx1">rx1</option>
            <option value="rx2">rx2</option>
          </select>
        </el-form-item>
         <el-form-item
          label="节点描述:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="isBindParkingLot"
        >
          <el-input autocomplete="off" v-model="editForm.description"></el-input>
        </el-form-item>
        <p>填写绑定信息 >></p>
       
          <el-form-item
          label="绑定车位:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
          <select  class="myselect" v-model="editForm.isBindParkingSpace">
            <option value=''>选择是否绑定车位</option>
            <option value="1">绑定</option>
            <option value="0">不绑定</option>
          </select>
        </el-form-item>
         <el-form-item
          label="选择停车场:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
          <select class="myselect" v-model="editForm.parkingLotId" @change="getLot($event)">
          <option value>选择绑定的停车场(全部)</option>
          <option :value="item.id" v-for="item in queryParkingLotdata">{{item.name}}</option>
        </select>
        </el-form-item>
         <el-form-item
          label="选择车位:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
          <select class="myselect" v-model="editForm.parkingSpaceId" >
          <option value=''>选择要绑定的车位</option>
          <option :value="item.parkingSpaceId" v-for="item in positions">{{item.parkingSpaceName}}</option>
        </select>
        </el-form-item>
      </el-form>
      <!-- 提交按扭 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormeditVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveEdit()" @close="dialogFormeditVisible = false">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { regionData, CodeToText, TextToCode } from "element-china-area-data"; //TextToCode数字转成汉字
import gateway from "./gateway-management.vue";
export default {
  name: "lockDetail",
  data() {
    return {
      dialogFormeditVisible: false,
      formLabelWidth: "125px",

      dialogVisible: false,
      dialogBind: false, //绑定弹出框
      parkingLotId:'',//停车场id
      detailId: "", //详情ID,
      parkingLockId:'',//车位id
      detailDatas: {}, //详情数据
      positions:'',//车位列表
       //查询所有停车场
      queryParkingLotdata: "",
      editForm:'',
      groups:'', 
    };
  },
  methods: {
    //详情数据
    getDetailData() {
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLock/findParkingLock",
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
              //车位接口
         this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLock/findParkingSpaceByParkingLotId",
          {parkingLotId:this.editForm.parkingLotId},
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
          this.positions = res.data.dataArray;
        })
        .catch(res => {
          console.log("err");
        });

        })
        .catch(res => {
          console.log("err");
        });
    },
    //删除弹出框
    open2() {
      var id =this.detailId;
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$http
          .post(
            this.GLOBAL.xgurl + "/park-api/park/parkingLock/deleteParkingLock",
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
            this.handleUserList(this.currentPage);
          })
          .catch(res => {
            console.log("err");
          });
      });
    },
    //解绑
    unbind(event) {
        var id = $(event.currentTarget).attr("id");
      var parkingLotId = $(event.currentTarget).attr("parkingLotId");
       var parkingLockId = $(event.currentTarget).attr("parkingLockId");
      this.$confirm('确定要删除与停车场的绑定吗？','提示',{
        confirmButtonText:'确定',
        cancelButtonText:'取消',
        type:'warning'
      }).then(()=>{
           this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLock/bindOrUnbindParkingLock",
          { id: id, parkingLotId: parkingLotId, isBindParkingSpace: 0 ,parkingSpaceId:parkingLockId},
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          this.getDetailData();
          console.log(res.data.dataArray);
          this.$message({
            message: res.data.errorMsg
          });
        
        })
        .catch(res => {
          console.log("err");
        });
      })
    

     
    },
    //绑定弹出框
    bind() {
      this.id = $(event.currentTarget).attr("id");
      this.dialogBind=true;    
    },
    //绑定功能
    bindsure(){
      this.dialogBind = false;
       this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLock/bindOrUnbindParkingLock",
          { id: this.id, parkingLotId: this.parkingLotId, isBindParkingSpace: 1,parkingSpaceId:this.parkingLockId },
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          this.getDetailData();
          console.log(res.data.dataArray);
          this.$message({
            message: res.data.errorMsg
          });
        })
        .catch(res => {
          console.log("err");
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
     //获取车位根据停车场
   getLot(event){
   
      var id=$(event.currentTarget).val();
       this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLock/findParkingSpaceByParkingLotId",
          {parkingLotId:id},
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
          this.positions = res.data.dataArray;
        })
        .catch(res => {
          console.log("err");
        });
   },
    //查询所有组列表
   getGroup(){
     this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLock/findAllGroup",
       
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
          this.groups = res.data.dataArray;
        })
        .catch(res => {
          console.log("err");
        });
   },
    //编辑保存
    saveEdit(){
        
        console.log(this.editForm);
        //this.editForm.picture=this.picture;
        //this.editForm.createUser=1;
         this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLock/addParkingLock",
          this.editForm,
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
          this.handleUserList(1);
        })
        .catch(res => {
          console.log("err");
        });
    },
  },
  created() {
    this.detailId=sessionStorage.getItem('id');
    this.detailId = this.$route.params.id;
     this.getGroup();
    this.getDetailData();
    this.queryParkingSpace();
    
  }
};
</script>
<style>
.lock-detail #add .el-dialog,.lock-detail #edit .el-dialog{
  display:flex!important;
  flex-direction:column!important;
  max-height: calc(70% );
   max-width: calc(100% - 30px)
}

.lock-detail #add .el-dialog__body,.lock-detail #edit .el-dialog__body{
      overflow:auto!important
}
.form-inline .control-label{
  width:100px!important;
}
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

