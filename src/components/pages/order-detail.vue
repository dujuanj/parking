<template>
  <div class="order-detail">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/order-management' }">订单管理</el-breadcrumb-item>
      <el-breadcrumb-item>订单详情</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>网关管理详情</el-breadcrumb-item> -->
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>
    <div>
      <div>
        <el-row>
          <el-button v-if='detailDatas.orderStatus==5' type="primary" @click="open2((detailDatas.totalFee).toFixed(2),detailDatas.id)" size="small">退款</el-button>
        </el-row>
        <!-- 版本信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">订单信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">订单编号:</label>
              <span>{{detailDatas.id}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">下单账号:</label>
              <span>{{detailDatas.userPhone}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">车牌号:</label>
              <span>{{detailDatas.licensePlate}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">预订时间:</label>
              <span>{{detailDatas.createTime|formatTime('YMD')}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">出场时间:</label>
              <span v-if="detailDatas.settlementTime==null ">--</span>
              <span v-else>{{detailDatas.settlementTime|formatTime('YMD')}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">停车时长:</label>
              <!-- <span>{{detailDatas.parkingTime < 60 ? detailDatas.parkingTime+"分钟":detailDatas.parkingTime==60?"1小时":detailDatas.parkingTime > 60? "1小时" + detailDatas.parkingTime-60+"分钟":''}}</span> -->
              <span v-if='detailDatas.parkingTime==0'>0分钟</span>
              <span v-else>{{ChangeHourMinutestr(detailDatas.parkingTime)}}分钟</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">停车费用:</label>
              <span>{{detailDatas.totalFee}}元</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">订单状态:</label>
              <span>{{detailDatas.orderStatus==1?"已预定":detailDatas.orderStatus==2?"停车中":detailDatas.orderStatus==3?"待支付":detailDatas.orderStatus==4?"已结束":detailDatas.orderStatus==5?"已取消":detailDatas.orderStatus==6?'待处理':detailDatas.orderStatus==7?"取消中":detailDatas.orderStatus==8?"已退款":''}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">支付方式:</label>
              <span>{{detailDatas.payType==0?"未支付":detailDatas.payType==1?"微信支付":detailDatas.payType==2?"支付宝支付":detailDatas.payType==3?"余额支付":''}}</span>
            </div>
            <!-- <div class="form-inline">
              <label for="totalNum" class="control-label">余额支付:</label>
              <span>{{detailDatas.remark}}</span>
            </div>-->
            <!-- <div class="form-inline">
              <label for="totalNum" class="control-label">支付宝支付:</label>
              <span>{{detailDatas.remark}}</span>
            </div>-->
            <div class="form-inline">
              <label for="totalNum" class="control-label">支付时间:</label>
              <span>{{detailDatas.payTime|formatTime('YMD') }}</span>
            </div>
          </div>
        </div>

        <!-- 车位信息 -->
        <div id="space_details" class="panel panel-default">
          <div class="panel-heading">停车场</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">停车场:</label>
              <span>{{detailDatas.parkingLotName}}</span>
            </div>
            <div class="form-inline">
              <label for="place-name" class="control-label">详细地址:</label>
              <span>{{detailDatas.address}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">停车位:</label>
              <span>{{detailDatas.parkingName}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">价格:</label>
              <span>{{detailDatas.price}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">免费时长:</label>
              <span>{{detailDatas.freeTime}}分钟</span>
            </div>
          </div>
        </div>
        <!-- 退款信息 -->
        <!-- <div id="space_details" class="panel panel-default">
          <div class="panel-heading">退款信息</div>
          <div class="panel-body">
            <div class="form-inline">
              <label for="place-name" class="control-label">退款时间:</label>
              <span>{{detailDatas.createUserName}}</span>
            </div>
            <div class="form-inline">
              <label for="area" class="control-label">退款金额:</label>
              <span>{{detailDatas.createLoginName}}</span>
            </div>
            <div class="form-inline">
              <label for="totalNum" class="control-label">操作人:</label>
              <span>{{detailDatas.createTime | formatTime('YMDHMS')}}</span>
            </div>
             <div class="form-inline">
              <label for="area" class="control-label">账号:</label>
              <span>{{detailDatas.createLoginName}}</span>
            </div>
             <div class="form-inline">
              <label for="area" class="control-label">操作时间:</label>
              <span>{{detailDatas.createLoginName}}</span>
            </div>
          </div>
        </div>-->
      </div>
    </div>

     <!-- 退款 -->
    <el-dialog title="退款" :visible.sync="dialogFormeditVisible" width="28%" id="edit">
      <el-form :model="ruleForm" ref="ruleForm" :rules="rules">
        <el-form-item
          label="退款金额:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="password"
        >
          <el-input autocomplete="off" v-model="refundmoney"></el-input>
          
        </el-form-item>
        <el-form-item
          label="退款原因:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="password"
        >
          <el-input type="textarea" v-model='content'></el-input>
          
        </el-form-item>
      </el-form>
      <!-- 提交按扭 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormeditVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitForm">确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>
<script>
import version from "./order-management.vue";
export default {
  name: "orderDetail",
  data() {
    return {
      dialogFormeditVisible: false,
      formLabelWidth: "105px",
      content:'',
      dialogVisible: false,
      dialogBind: false, //绑定弹出框
      parkingLotId: "", //停车场id
      detailId: "", //详情ID,
      detailDatas: {}, //详情数据
      refundmoney:'',
      editForm: "", //编辑回显数据
      server: "" //服务器网卡数据
    };
  },
  methods: {
    //详情数据
    getDetailData() {
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/order/orderDetail",
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
          this.editForm = res.data.dataArray;
        })
        .catch(res => {
          console.log("err");
        });
    },
    open2(refundmoney,id){ //退款
      this.dialogFormeditVisible=true;
      this.refundmoney=refundmoney; 
      this.id=id;     
    },
    //上传excel
    beforeUpload(file) {
      console.log(file);
      var formdata = new FormData(); //创建一个表单
      formdata.append("fileUrl", file);
      //   formdata.append("className", "TGateway");
      let _this = this;
      //调用接口传文件
      _this.$http
        .post(_this.GLOBAL.xgurl + "/park-api/park/file/getUrl", formdata, {
          headers: {
            "Content-Type": "application/json;charset=UTF-8"
          }
        })
        .then(res => {
          console.log(res.data);
          this.$message({
            type: "success",
            message: res.data.errorMsg
          });
        })
        .catch(res => {
          console.log("err");
        });
    },

    submitForm(){
      this.$http
          .post(
            this.GLOBAL.xgurl + "/park-api/order/orderRefound",
            {
              id: this.id,
              refundmoney:this.refundmoney,
              content:this.content
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
              message: res.data.errorMsg
            });
            this.id='';
           this.handleUserList(currentPage)
          })
          .catch(res => {
            console.log("err");
          });
    },
    ChangeHourMinutestr(str) {
      if (str !== "0" && str !== "" && str !== null) {
        return (
          (Math.floor(str / 60).toString().length < 2
            ? "" + Math.floor(str / 60).toString()
            : Math.floor(str / 60).toString()) +
          "小时" +
          ((str % 60).toString().length < 2
            ? "" + (str % 60).toString()
            : (str % 60).toString())
        );
      } else {
        return "";
      }
    }
  },
  created() {
    this.detailId = sessionStorage.getItem("id");
    this.detailId = this.$route.params.id;
    this.getDetailData();
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

