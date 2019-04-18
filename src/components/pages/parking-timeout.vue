<template>
  <div class="timeout">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>停车超时设置</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动列表</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>

    <div class="rightWrap">
      <!-- 搜索区 -->
      
      <!-- 添加删除按扭功能 -->
      <div>
        <el-row style="text-align:left;margin-top:15px;">

          <div><span style='color:red'>*</span>当停车超时XX小时后，向预订车位的用户发送停车超时提醒</div>
            <el-button type="primary" @click="dialogFormVisible = true" size="small" style='margin-top:10px;'>添加</el-button>
         
        </el-row>
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
              <th>时长</th>
              <th>单位</th>
              <th>创建人</th>
              <th>创建日期</th>
              <th>操作</th>           
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item,index) in tableData">
              <!-- 	<td><input type="checkbox" :value='item.id' v-model='ids'/></td> -->
              <td style="width:5%">{{(currentPage)*10+(index+1)}}</td>
              <td>{{item.time}}</td>
              <td>{{item.timeUnit==1?"秒":item.timeUnit==2?"分":item.timeUnit==3?"小时":item.timeUnit==4?"天":''}}</td>
              <td>{{item.createUser}}</td>
               <td>{{item.createTime | formatTime('YMDHMS') }}</td>
              <td>
                    <el-button size="mini" v-bind:id="item.id" :time='item.time'  @click="getDetail($event)">编辑</el-button>
                    <el-button size="mini" @click="open2($event)">删除</el-button>
              </td>
            </tr>
          </tbody>
        </table>

        <!-- <el-pagination
          background
          layout="total, prev, pager, next, jumper"
          :total="total"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-size="pagesize"
        ></el-pagination> -->
      </div>
    </div>

    <!-- 添加弹框 -->
    <el-dialog title="添加时长" :visible.sync="dialogFormVisible" width="35%" id="add">
    
      <el-form :model="ruleForm" ref="ruleForm" :rules="rules">
       
        
        

        <el-form-item
          label="输入时长:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
         <el-input v-model="ruleForm.time" placeholder="（大于0的纯数字）" style='width:300px'></el-input>小时
        </el-form-item>
      </el-form>
      <!-- 提交按扭 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitForm('ruleForm')">确 定</el-button>
      </div>
    </el-dialog>

   <!-- 编辑弹框 -->

    <el-dialog title="添加时长" :visible.sync="dialogFormeditVisible" width="35%" id="add">
    
      <el-form :model="ruleForm" ref="ruleForm" :rules="rules">
       
        
        

        <el-form-item
          label="输入时长:"
          :label-width="formLabelWidth"
          style="color:#000"
          prop="parkingLotId"
        >
         <el-input v-model="time" placeholder="（大于0的纯数字）" style='width:300px'></el-input>时
        </el-form-item>
      </el-form>
      <!-- 提交按扭 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormeditVisible = false">取 消</el-button>
        <el-button type="primary" @click='editSave()'>确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
import XLSX from 'xlsx'
export default {
  name: "timeout",
  data() {
    return {
      id:'',
      time:'',
      // 分页数据
      currentPage: 0, //当前页
      total: "", //总数
      pagesize: 10, // 每页数据
      formLabelWidth: "105px",
      dialogTableVisible: false,
      dialogFormVisible: false,
      dialogFormeditVisible: false,   
      dialogVisible: false,    
      tableData: "", //列表数据
    
      //添加数据
      ruleForm: {
        time:'',
        timeUnit:3
      },
      
      //添加验证规则
      rules: {
        // name: [
        //   {
        //     required: true,
        //     message: "请输入网关名称，不能为空",
        //     trigger: "blur"
        //   }
        // ],
       
      }
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
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/order/showTimeOutSet",
          {
            pageNumber: currentpage,
            pageSize: this.pagesize,
          },
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res);
          console.log(res.data.dataArray);
          // this.total = res.data.dataArray.totalCount;
          // console.log(this.total);
          this.tableData = res.data.dataArray;
        })
        .catch(res => {
          console.log("err");
        });
    },
  
    //添加
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          //alert("submit!");
          var datas = this.$refs[formName].model;
          datas.createUser=sessionStorage.getItem("managerId");
          datas.time=parseInt(this.ruleForm.time);
          console.log(datas);
          this.$http
            .post(this.GLOBAL.xgurl + "/park-api/order/createTimeOutSet", datas, {
              headers: {
                "Content-Type": "application/json;charset=UTF-8"
              }
            })
            .then(res => {
              console.log(res.data);

              //$("#add").hide();
              this.$message.success(res.data.dataArray);
              this.dialogFormVisible = false;
              this.handleUserList(0);
              
            })
            // .catch(res => {
            //   console.log("err");
            // });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //编辑
   getDetail(event) {
      //编辑回显功能
    
      this.id = $(event.currentTarget).attr("id");
      console.log(this.id);
      this.time= $(event.currentTarget).attr("time");
      this.dialogFormeditVisible = true;
    },
    //编辑保存
    editSave(){
         this.$http
            .post(this.GLOBAL.xgurl + "/park-api/order/updateTimeOutSet", {
                id:this.id,
                time:this.time,
                timeUnit:3
            }, {
              headers: {
                "Content-Type": "application/json;charset=UTF-8"
              }
            })
            .then(res => {
              console.log(res.data);

              //$("#add").hide();
              this.$message.success(res.data.dataArray);
              this.dialogFormVisible = false;
              this.handleUserList(0);
              
            })
          
    },
   //删除弹出框
    open2(event) {
      var id = $(event.currentTarget)
        .prev()
        .attr("id");
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$http
          .post(
            this.GLOBAL.xgurl + "/park-api/order/deleteTimeOutSet",
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
              message: res.data.dataArray
            });
            this.handleUserList(1);
          })
          .catch(res => {
            console.log("err");
          });
      });
     
    },
  },

  mounted: function() {},
  components: {},
  created() {
    this.handleUserList(this.currentPage);
  
   
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
