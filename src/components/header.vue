<template>
  <div class="header">
    <div class="left">
      <img src="../assets/img/hiss-logo.png" alt>
      <span style="font-size:16px;">HISS</span>
    </div>
    <div class="right">
      <el-menu
        background-color="#545c64"
        class="el-menu-demo"
        mode="horizontal"
        text-color="#fff"
        active-text-color="#ffd04b"
      >
        <el-menu-item index="1" style="font-size:18px;">HISS 停车后台管理系统</el-menu-item>
        <el-submenu index="2" style="float:right">
          <template slot="title"> <span id='title'>{{username}}</span></template>
          <el-menu-item index="2-1" @click="dialogFormVisible = true">修改密码</el-menu-item>
          <el-menu-item index="2-2" @click="Cancellation = true">注销登陆</el-menu-item>
        </el-submenu>
      </el-menu>
    </div>
    <!-- 修改密码弹出框 -->
    <el-dialog title="修改密码" :visible.sync="dialogFormVisible" width="23%">
      <el-form :model="ruleForm" ref="ruleForm" :rules="rules">
        <el-input placeholder="设置新的密码" v-model="ruleForm.password"></el-input>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false || changePwd('ruleForm')">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 注销弹出框 -->
    <el-dialog title="注销登录" :visible.sync="Cancellation" width="23%">
      <el-form>
        <span>确定要注销登录吗?</span>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="Cancellation = false">取 消</el-button>
        <el-button type="primary" @click="Cancellation = false || cancellation()">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "header",
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      dialogFormVisible: false,
      Cancellation: false,
      formLabelWidth: "75px",
      username: sessionStorage.getItem("username"),
      managerId: sessionStorage.getItem("managerId"),
      ruleForm: {
        // username: "",
        // loginName: "",
        password: ""
        // enable: "",
        // roleId: "",
        // createUser: ""
      },
      //添加验证规则
      rules: {
        password: [
          {
            required: true,
            message: "请输入用户密码，不能为空",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    goIndex: function() {
      alert(222);
      this.$router.push({ path: "/smart" });
    },
    // 修改密码
    changePwd(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          //alert("submit!");
          var datas = this.$refs[formName].model;
          datas.managerId = this.managerId;
          datas.password = encrypt(this.ruleForm.password);
          console.log(datas);
          this.$http
            .post(
              this.GLOBAL.xgurl + "/park-api/park/sys/resetPassword",
              datas,
              {
                headers: {
                  "Content-Type": "application/json;charset=UTF-8"
                }
              }
            )
            .then(res => {
              console.log(res.data);

              this.$message.success(res.data.errorMsg);
              this.handleUserList(this.currentPage);
              this.dialogFormeditVisible = false;
              this.$refs[formName].resetFields();
            })
            .catch(res => {
              console.log("err");
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    // 注销登陆
    cancellation: function() {
      this.$http
        .post(
          this.GLOBAL.xgurl + "/park-api/park/sys/cancleRegister",
          { managerId: this.managerId },
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
          if(res.data.isSuccess=='success'){
            setTimeout(this.jumpindex,1500);
           
            sessionStorage.setItem('username','');
          }
         
           
        })
        .catch(res => {
          console.log("err");
        });
    },
    jumpindex(){
       this.$router.push("/");
    }
  },
  created() {
    console.log(sessionStorage.getItem("username"));
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.header {
  width: 100%;
  height: 60px;
}
.left {
  width: 10.6%;
  height: 100%;
  background: #1f262d;
  float: left;
  color: #fff;
  line-height: 60px;
}
.left img {
  vertical-align: middle;
  margin-left: 27px;
  width: 26px;
  height: 26px;
  margin-right: 5px;
  margin-bottom: 5px;
}
.right {
  float: left;
  width: 89.4%;
}
</style>
