<template>
  <div class="index-parking login_bg" :style="objHeight">
    <div class="login_wrap">
      <div class="login_text"></div>
      <p class="Subtitle">停车有位，让城市生活更美好</p>
      <div class="login_form">
        <!-- <h3 style="font-size:26px;">后台登陆系统</h3> -->
        <!-- 表单 -->
        <el-form
          :model="ruleForm"
          :rules="rules"
          ref="ruleForm"
          label-width="100px"
          class="demo-ruleForm"
        >
          <!-- 用户名 -->
          <el-form-item label="用户名" prop="loginName" id="user">
            <el-input v-model="ruleForm.loginName"></el-input>
          </el-form-item>
          <!-- 密码 -->
          <el-form-item label="密码" prop="password" id="pwd">
            <el-input v-model="ruleForm.password"></el-input>
          </el-form-item>
          <!-- 验证码 -->
          <el-form-item label="验证码" prop="code" style="text-align:left;" id="code">
            <el-input v-model="ruleForm.code" style="width:55%"></el-input>
            <!-- <a href="javascript:;" title="点击更换验证码"> -->
            <img
              id="imgVerify"
              src
              alt="更换验证码"
              :src="randomcode"
              style="width: 32%;height:49px;float:right"
              @click="getVerify()"
            >
            <!-- </a> -->
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm('ruleForm')" style="width:100%;">登陆</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "loginParking",
  data() {
    return {
      randomcode: "", //随机验证码
      seesioncode: "", //session中的验证码
      objHeight: {
        height: window.innerHeight + "px"
      },
      ruleForm: {
        loginName: "",
        password: "",
        code: ""
      },
      rules: {
        loginName: [
          { required: true, message: "请输入用户名称", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入用户密码", trigger: "blur" }
        ],

        code: [{ required: true, message: "请输入验证码", trigger: "blur" }]
      }
    };
  },
  methods: {
    //登陆接口
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          //alert("submit!");
           //this.$router.push("/index-parking");//接口通，删除
          var datas = this.$refs[formName].model;
          console.log(datas);
         
          //验证-验证码
                this.$http
        .get(
          this.GLOBAL.xgurl +
            "/park-api/park/authCode/checkValidateCode?validateCode=" +
           
            this.ruleForm.code,

          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            },
            //  withCredentials:true,
            //  crossDomain: true,
           
          }
        )
        .then(res => {
          console.log(res);
          console.log(res.data);
          // if (res.data.isSuccess == 'success') {
            if(true){
              console.log(127)
           //登陆接口
            datas.password=encrypt(this.ruleForm.password);
            this.$http
              .post(
                this.GLOBAL.xgurl + "/park-api/park/sys/loginManager",
                datas,
                {
                  headers: {
                    "Content-Type": "application/json;charset=UTF-8"
                  }
                }
              )
              .then(res => {
                console.log(res.data);
                if (res.data.isSuccess == "success") {
                  sessionStorage.setItem('username',this.ruleForm.loginName);
                  sessionStorage.setItem('password',this.ruleForm.password);
                  sessionStorage.setItem('managerId',res.data.dataArray);
                  this.$router.push("/index-parking");
                } else {
                  this.$message({
                    type: "success",
                    message: res.data.errorMsg
                  });
                   
                }
                this.$refs[formName].resetFields();
              })
              .catch(res => {
                console.log("err");
                //this.$router.push("/index-parking");
              });
          } else {
            this.$message({
              type: "success",
              message: '验证码错误，请重新输入'
            });
          }
        })
        .catch(res => {
          console.log("err");
          //不报500删除
          //this.$router.push("/index-parking");
        });
          
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    //获取验证码
    getVerify(event) {
      //this.randomcode --验证码路径
      // this.seesioncode=sessionStorage.getItem('RANDOMVALIDATECODEKEY');
      // console.log(this.seesioncode);
      // console.log(
      //   // this.GLOBAL.xgurl +
      //     // "/park-api/park/authCode/getValidateCode?" +
      //     'http://192.168.11.22:47073/authCode/checkValidateCode'+
      //     Math.random()
      // );
      // this.randomcode =
      //   this.GLOBAL.xgurl +
      //   "/park-api/park/authCode/getValidateCode?" +
      //   //  '/authCode/getValidateCode?'+
      //   Math.random();
       this.$http
        .get(
          this.GLOBAL.xgurl + "/park-api/park/authCode/getValidateCode",

          {
            headers: {
              "Content-Type": "application/json;charset=UTF-8"
            },
             withCredentials:true,
             crossDomain: true,
          }
        )
        .then(res => {
          console.log(res);
        
          this.randomcode = "data:image/jpg;base64,"+res.data.dataArray.src
        })
        .catch(res => {
          console.log("err");
        });

    },
    //验证码校验方法
    checkCode() {

    }
  },
  created() {
    this.getVerify();
  }
};
</script>


<style scoped>
.login_bg {
  background: url(../assets/img/login_bg.jpg) no-repeat;
  background-size: 100%;
  width: 100%;
}
.login_wrap {
  width: 572px;
  margin: 0 auto;
  padding-top: 210px;
  color: #fff;
}
.Subtitle {
  color: #fff;
  font-size: 24px;
  margin: 0 auto;
  text-align: center;
  margin-top: 19px;
}
.login_text {
  width: 165%;
  height: 150px;
  background: url(../assets/img/login_text.png) no-repeat;
  background-size: 100% 100%;
  margin-left:  -27%;
}
.login_form {
  width: 400px;
  border: 1px solid #529fd8;
  color: #fff;
  margin: 62px auto;
  text-align: center;
  padding: 15px 30px 30px 0;
}
h3 {
  font-weight: normal;
  font-size: 26px;
  margin-bottom: 18px;
  margin-left: 32px;
}
</style>
