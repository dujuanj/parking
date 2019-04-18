<template>
  <div class="repair">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>故障报修管理</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动列表</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>

    <div class="rightWrap">
      <!-- 搜索区 -->
      <div style="margin-top: 15px;text-align:left;">
        <el-input placeholder="输入报修人手机号查询" class="input-with-select" v-model="userPhone">
          <el-button slot="append" icon="el-icon-search" @click="handleUserList(1)"></el-button>
        </el-input>
         <select class="myselect" v-model="type" @change="handleUserList(1)">
          <option value='0'>选择故障类型(全部)</option>
          <option value="1" >订单异常</option>
          <option value="2" >升锁异常</option>
          <option value="3" >降锁异常</option>
        </select>
          <select class="myselect" v-model="parkingSpaceLotId" @change="handleUserList(1)">
          <option value=''>选择所属停车场(全部)</option>
          <option :value="item.id" v-for="item in queryParkingLotdata">{{item.name}}</option>
        </select>
         <select class="myselect" v-model="status" @change="handleUserList(1)">
          <option value='0'>选择报修状态(全部)</option>
          <option value="2" >待处理</option>
          <option value="1" >已处理</option>
        
        </select>
        <!-- <select class="myselect" v-model="parkingSpaceLotId" @change="handleUserList(1)">
          <option value=''>选择设备类型(全部)</option>
          <option value="">网关</option>
          <option value="">地锁</option>
        </select> -->
       
<!--         
        <el-date-picker
        @change="searchTime"
      v-model="stime"
      type="datetimerange"
      align="right"
      start-placeholder="开始日期"
      end-placeholder="结束日期"
      value-format="timestamp"
      :default-time="['00:00:00', '23:59:00']">
      
    </el-date-picker> -->

        <el-button type="info" size="small" @click="reset">重置</el-button>
      </div>
      <!-- 添加删除按扭功能 -->
      <div>
       
      </div>
      <!-- 表单 -->
      <div>
        <table class="table table-striped" style="width: 97%;margin-top:10px;" v-cloak>
          <thead>
            <tr>
              <!--   <th>
                                            <input type="checkbox"  id="all_checked"/>
              全选</th>-->
              <th>序号</th>
              <th>故障类型</th>
              <th>停车场</th>
              <th>车位号</th>
              <th>订单编号</th>
              <th>报修人手机号</th>
              <th>故障描述</th>
              <th>报修时间</th>
              <th>状态</th>
              <th>操作</th>
             

            
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item,index) in tableData">
              <!-- 	<td><input type="checkbox" :value='item.id' v-model='ids'/></td> -->
              <td style="width:5%">{{(currentPage-1)*10+(index+1)}}</td>
              <!-- 故障类型 -->
              <td>{{item.type==1?"订单异常":item.type==2?"升锁异常 ":item.type==3?"降锁异常":''}}</td>
              <td>{{item.parkingLotName}}</td>
              <td>{{item.parkingName}}</td>
              <td>{{item.orderId}}</td>
             

              <td>{{item.userPhone}}</td>
              <td v-bind:title='item.description'>
                <span style="display:inline-block;width: 150px;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;cursor:pointer"> {{item.description}}</span>
               </td>
             
              <td>{{item.createTime|formatTime('YMD')}}</td>
              
              <td>{{item.status==1?"已处理":item.status==2?"待处理":""}}</td>
              <td>
                 <el-button size="mini" v-bind:id="item.id" v-bind:types="item.type" @click="handleEdit($event)" v-if='item.status==2'>处理</el-button>
                <el-button size="mini" @click="getDescribe($event)" :detail-id="item.id">详情</el-button>
               
              </td>
            </tr>
          </tbody>
        </table>

        <el-pagination
          background
          layout="total, prev, pager, next, jumper"
          :total="total"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-size="pagesize"
        ></el-pagination>
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
import XLSX from 'xlsx'
export default {
  name: "repair",
  data() {
    return {
      //筛选
      userPhone:'',
      parkingSpaceLotId:'',
      status:'0',
      //时间转换
      stime:'',
      startTime:'',
      endTime:'',
      endTime:'',//处理时间

      // 分页数据
      currentPage: 1, //当前页
      total: "", //总数
      pagesize: 10, // 每页数据
      //
      formLabelWidth: "115px",
      dialogTableVisible: false,
      dialogFormVisible: false,
      dialogFormeditVisible1:false,
      dialogFormeditVisible: false,
      //查询所有停车场
      queryParkingLotdata: "",
      dialogVisible: false,
      dialogImageUrl:'',
    
      tableData: "", //列表数据
      type:'0',//订单类型
      result:'',//处理结果
      types:'',//处理功能的类型
    };
  },
  methods: {
    // 初始页currentPage、初始每页数据数pagesize和数据data
    handleSizeChange: function(size) {
      this.pagesize = size;
      console.log(this.pagesize); //每页下拉显示数据
    },
    handleCurrentChange: function(currentPage) {
      this.currentPage = currentPage;
      console.log(this.currentPage); //点击第几页
      this.handleUserList(this.currentPage);
    },
    searchTime:function(){
        console.log(this.stime);
       
        this.startTime=this.stime[0];
        this.endTime=this.stime[1];
        this.handleUserList(1);
    },
    //列表数据
    handleUserList(currentpage) {
      currentpage--;
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/order/allRepair",
          {
            pageNumber: currentpage,
            pageSize: this.pagesize,
            parkingSpaceLotId:this.parkingSpaceLotId,
            userPhone:this.userPhone,
            type:this.type,
            status:this.status

          },
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
          this.total = res.data.dataArray.totalCount;
          console.log(this.total);
          this.tableData = res.data.dataArray.datas;
        })
        .catch(res => {
          console.log("err");
        });
    },
    //重置
    reset() {     
        this.parkingSpaceLotId='',
        this.type='0',
        this.status='0',      
        this.userPhone='',
        this.handleUserList(1)        
    },
     //详情
    getDescribe(event) {
      var id = $(event.currentTarget).attr("detail-id");
      this.$router.push({
        name: "repairDetail",
        params: {
          id: id
        }
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
    // 订单处理
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

  mounted: function() {},
  components: {},
  created() {
    this.handleUserList(this.currentPage);
    this.queryParkingSpace();
   
   
  }
};
</script>


<style scoped>
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
  margin-bottom: 22px !important;
}
.el-dialog__wrapper {
  text-align: left !important;
}
.el-input-group {
  width: 16% !important;
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
</style>
