<template>
  <div class="report">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>报表统计</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动列表</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>

    <div class="rightWrap">
      <el-row :gutter="20" style="margin-right:0;margin-top:20px;">
        <!-- 停车场统计 -->
        <el-col :span="8">
          <div class="grid-content bg-purple clear-fix" style="padding:5px;padding-bottom:15px;">
            <h4 style="margin-bottom:20px">停车场统计</h4>
            <div style="display:flex">
              <div class="fl ">
                <span>总数量</span>
                <br>
                <span>{{parkingLotFormVO.totalNum}}</span>
              </div>
              <div class="fl ">
                <span>1-50个车位</span>
                <br>
                <span>{{parkingLotFormVO.oneToFiftyNum}} | {{parkingLotFormVO.oneToFiftyPercent}}</span>
              </div>
              <div class="fl ">
                <span>51-200个车位</span>
                <br>
                 <span>{{parkingLotFormVO.fiftyToTwoHundredNum}} | {{parkingLotFormVO.fiftyToTwoHundredPercent}}</span>
              </div>
              <div class="fl ">
                <span>201个以上车位</span>
                <br>
                <span>{{parkingLotFormVO.twoHundredNum}} | {{parkingLotFormVO.twoHundredPercent}}</span>
              </div>
            </div>
          </div>
        </el-col>
        <!-- 车位统计 -->
        <el-col :span="8">
          <div class="grid-content bg-purple clear-fix" style="padding:5px;padding-bottom:15px;">
            <h4 style="margin-bottom:20px">车位统计</h4>
            <div style="display:flex">
              <div class="fl" >
                <span>总数量</span>
                <br>
                <span>{{parkingSpaceForVO.totalNum}}</span>
              </div>
              <div class="fl" >
                <span>绑定业主</span>
                <br>
                <span>{{parkingSpaceForVO.bindUserNum}} | {{parkingSpaceForVO.bindUserPercent}}</span>
              </div>
              <div class="fl" >
                <span>未绑定业主</span>
                <br>
                <span>{{parkingSpaceForVO.unBindUserNum}} | {{parkingSpaceForVO.unBindUserPercent}}</span>
              </div>
            </div>
          </div>
        </el-col>
        <!-- 累计订单统计 -->
        <el-col :span="8">
          <div class="grid-content bg-purple clear-fix" style="padding:5px;padding-bottom:15px;">
            <h4 style="margin-bottom:20px">累计订单统计</h4>
            <div style="display:flex">
              <div class="fl" >
                <span>下单数量</span>
                <br>
                <span>{{countVO.allOrderNum}}</span>
              </div>
              <div class="fl" >
                <span>退单数量</span>
                <br>
                <span>{{countVO.allRefundNum}}</span>
              </div>
              <div class="fl" >
                <span>退单率</span>
                <br>
                <span>{{countVO.allPercent}}%</span>
              </div>
            </div>
          </div>
        </el-col>
        <!-- 今日订单统计 -->
        <el-col :span="8">
         <div class="grid-content bg-purple clear-fix" style="padding:5px;padding-bottom:15px;margin-top:20px;">
            <h4 style="margin-bottom:20px">今日订单统计</h4>
            <div style="display:flex">
              <div class="fl" >
                <span>下单数量</span>
                <br>
                <span>{{countVO.dayOrderNum}}</span>
              </div>
              <div class="fl" >
                <span>退单数量</span>
                <br>
                <span>{{countVO.dayRefundNum}}</span>
              </div>
              <div class="fl" >
                <span>退单率</span>
                <br>
                <span>{{countVO.dayPercent}}%</span>
              </div>
            </div>
          </div>
        </el-col>
        <!-- 累计收入统计 -->
        <el-col :span="8">
         <div class="grid-content bg-purple clear-fix" style="padding:5px;padding-bottom:15px;margin-top:20px;">
            <h4 style="margin-bottom:20px">累计收入统计</h4>
            <div style="display:flex">
              <div class="fl" >
                <span>总收入（元）</span>
                <br>
                <span>{{countVO.allIncome}}</span>
              </div>
              <div class="fl" >
                <span>业主车位（元）</span>
                <br>
                <span>{{countVO.allUserIncome}}</span>
              </div>
              <div class="fl" >
                <span>停车场车位（元）</span>
                <br>
                <span>{{countVO.allParkingIncome}}</span>
              </div>
            </div>
          </div>
        </el-col>
        <!-- 今日收入统计 -->
        <el-col :span="8">
         <div class="grid-content bg-purple clear-fix" style="padding:5px;padding-bottom:15px;margin-top:20px;">
            <h4 style="margin-bottom:20px">今日收入统计</h4>
            <div style="display:flex">
              <div class="fl" >
                <span>总收入（元）</span>
                <br>
                <span>{{countVO.dayIncome}}</span>
              </div>
              <div class="fl" >
                <span>业主车位（元）</span>
                <br>
                <span>{{countVO.dayUserIncome}}</span>
              </div>
              <div class="fl" >
                <span>停车场车位（元）</span>
                <br>
                <span>{{countVO.dayParkingIncome}}</span>
              </div>
            </div>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
export default {
  name: "report",
  data() {
    return {
      parkingLotFormVO:'',
      orderFormVO:'',
      countVO:'',
      parkingSpaceForVO:'',
    };
  },

  methods: {
    reportDatas: function() {
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/parkingLot/reportForm",

          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
          this.parkingLotFormVO = res.data.dataArray.parkingLotFormVO;
          this.orderFormVO=res.data.dataArray.orderFormVO;
          this.countVO=res.data.dataArray.countVO;
          this.parkingSpaceForVO=res.data.dataArray.parkingSpaceForVO;
          console.log(this.parkingLotFormVO);
          console.log(this.orderFormVO);
          console.log(this.countVO);
        })
        .catch(res => {
          console.log("err");
        });
    }
  },

  mounted: function() {},
  components: {},
  created() {
    this.reportDatas();
  }
};
</script>


<style scoped>
.clear-fix {
  overflow: hidden;
}
.fl {
  /* float: left; */
  flex: 1;
}
.mr_5 {
  margin-right: 5%;
}
.el-row {
  margin-bottom: 20px;
  &:last-child {
    margin-bottom: 0;
  }
}
.el-col {
  border-radius: 4px;
}
.bg-purple-dark {
  background: #99a9bf;
}
.bg-purple {
  background: #d3dce6;
}
.bg-purple-light {
  background: #e5e9f2;
}
.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
.row-bg {
  padding: 10px 0;
  background-color: #f9fafc;
}
.home {
  font-size: 28px;
}
.rightWrap {
  margin-left: 20px !important;
}
</style>
