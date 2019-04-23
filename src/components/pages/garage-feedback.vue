<template>
  <div class="garage">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index-parking' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>车场信息反馈管理</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动列表</el-breadcrumb-item>
      <!-- <el-breadcrumb-item>活动详情</el-breadcrumb-item>-->
    </el-breadcrumb>

    <div class="rightWrap">
      <!-- 搜索区 -->
      <div style="margin-top: 15px;text-align:left;">
        <select class="myselect" v-model="parkingLotName" @change="handleUserList(1)">
          <option value=''>选择所属停车场(全部)</option>
          <option :value="item.name" v-for="item in queryParkingLotdata">{{item.name}}</option>
        </select>
        <!-- 错误类型 -->
        <select v-model="errorType" @change="handleUserList(1)" class="myselect">
          <option value>选择错误类型(全部)</option>
          <option value="1">停车场不存在</option>
          <option value="2">停车场不对外开放</option>
          <option value="3">空闲车位状态错误</option>
          <option value="4">位置错误</option>
          <option value="5">收费信息错误</option>
          <option value="6">停车场名称错误</option>
          <option value="7">营业时段不符</option>
          <option value="8">免费时长</option>
          <option value="9">其它</option>
        </select>
       
       

        <el-button type="info" size="small" @click="reset">重置</el-button>
      </div>
      <!-- 添加删除按扭功能 -->
      <div>
        <el-row style="text-align:left;margin-top:15px;">
                  <!-- <el-button type="primary" @click="dialogFormVisible = true" size="small">添加</el-button> -->
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
              <th>停车场</th>
              <th>错误类型</th>
              <th>照片凭证</th>
              <th>问题描述</th>
              <th>反馈人账号</th>
              <th>反馈时间</th>
              
              <th>状态</th>
              <th style="width:170px;">操作</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item,index) in tableData">
              <!-- 	<td><input type="checkbox" :value='item.id' v-model='ids'/></td> -->
              <td style="width:5%">{{(currentPage-1)*10+(index+1)}}</td>
              <td>{{item.parkingLotName}}</td>
              <td>{{item.errorType==1?"停车场不存在":item.errorType==2?"停车场不对外开放":item.errorType==3?"空闲车位状态错误":item.errorType==4?"位置错误":item.errorType=5?"收费信息错误":item.errorType==6?"停车场名称错误":item.errorType==7?"营业时段不符":item.errorType==8?"免费时长":item.errorType==9?"其它":"--"}}</td>
              <!-- <td>{{item.errorType}}</td> -->
              <td>
                <a href='javascript:;' :url='item.pictureOne' :pic='item.pictureTwo' @click="open5($event)"> 查看</a>
              </td>
              <td>{{item.content}}</td>
              <td>{{item.phone}}</td>
              <td>{{item.createTime | formatTime('YMDHMS')}}</td>
              <td>{{item.status==0?"未解决":item.status==1?"已处理":''}}</td>
              <td style="width:15%">
                <el-button size="mini" @click="getDescribe($event)" :detail-id="item.id">详情</el-button>
                <el-button size="mini" v-bind:id="item.id" @click="handleEdit($event)" v-if='item.status==0'>处理</el-button>
              </td>
            </tr>
          </tbody>
        </table>

        <!-- <el-pagination
          background
          layout="total,  prev, pager, next, jumper"
          :total="total"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-size="pagesize"
        ></el-pagination> -->
      </div>
    </div>

    <!-- 处理弹出框 -->
    <el-dialog title="处理车场信息反馈" :visible.sync="dialogFormeditVisible" width="35%" id="edit">
      <el-form :model="editForm" ref="ruleForm" :rules="rules">
       <el-form-item label="填写:" :label-width="formLabelWidth" style="color:#000"  prop="result">
         <textarea name="" id="" cols="55" rows="3" v-model="result"></textarea>
        </el-form-item>
      </el-form>
      <!-- 提交按扭 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormeditVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveEdit('ruleForm')">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import XLSX from 'xlsx'
export default {
  name: "garage",
  data() {
    return {
      //筛选
      parkingLotName:'',
      errorType:'',
     
      //查询所有停车场
      queryParkingLotdata: "",
      result:'',//处理内容
      id:'',//每条数据id
      // 分页数据
      currentPage: 1, //当前页
      total: "", //总数
      pagesize: 10, // 每页数据
      //
      formLabelWidth: "100px",
      dialogTableVisible: false,
      dialogFormVisible: false,
      dialogFormeditVisible: false,
      value7: "",
      dialogVisible: false,
      dialogImageUrl:'',
      imgList:[],//回显的图片
      editTime:'',//开始结束日期
      tableData: "", //列表数据
      //添加数据
      ruleForm: {
        result: [
           {
            required: true,
            message: "请输入反馈信息，不能为空",
            trigger: "blur"
          }
        ]
       
      },
     
      //添加验证规则
      rules: {
        result: [
          {
            required: true,
            message: "请输入反馈信息，不能为空",
            trigger: "blur"
          }
        ]
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
    //列表数据
    handleUserList(currentpage) {
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/infofeedback/list",
          {
            parkingLotName: this.parkingLotName,
            errorType: this.errorType,
          },
          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            }
          }
        )
        .then(res => {
          console.log(res.data.dataArray);
        //   this.total = res.data.dataArray.totalCount;
        //   console.log(this.total);
          this.tableData = res.data.dataArray;
        })
        .catch(res => {
          console.log("err");
        });
    },
    //重置
    reset() {
       this.parkingLotName="",
       this.errorType= "",
       this.handleUserList()
    },
    
    //处理确定
    saveEdit(formName){
         this.$refs[formName].validate(valid => {
            if (valid) {
                 this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/infofeedback/handler",
          {
            id:this.id,
            result:this.ruleForm.result,
            createUser:sessionStorage.getItem("managerId"),
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
            }
          });
        
    },


    handleChange(value) {
      console.log(value);
      console.log(value.length);
      var len = value.length;

      for (var i = 0; i < len; i++) {
        this.cityOptions.push(CodeToText[value[i]]);
      }
      console.log(this.cityOptions); // ["北京市", "市辖区", "朝阳区", __ob__: Observer]
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
      console.log(this.dialogImageUrl);
    },
    //处理
    handleEdit(event) {
      this.dialogFormeditVisible = true;
      this.id= $(event.currentTarget).attr("id");
    },
  

    //详情
    getDescribe(event) {
      var id = $(event.currentTarget).attr("detail-id");
      this.$router.push({
        name: "garageDetail",
        params: {
          id: id
        }
      });
    },
     
     open5(event) {
       var url=$(event.currentTarget).attr('url');
       var pic=$(event.currentTarget).attr('pic');
        this.$alert('<img style="width:180px;height:160px;display: inline-block;margin-right:10px;" src="'+url+ '"/><img style="width:180px;height:160px;display: inline-block;" src="'+pic+ '"/>', '查看照片', {
          dangerouslyUseHTMLString: true,
          closeOnClickModal:true
        });
      }
  },

  mounted: function() {},
  components: {},
  created() {
    this.handleUserList(this.currentPage);
    this.queryParkingSpace();
    this. findAllServer();
  }
};
</script>


<style scoped>
.el-message-box{
  display: inline-block;
  width:530px!important;
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
