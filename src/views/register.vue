<!--
 * @Author: xujintai
 * @Date: 2021-04-18 12:44:28
 * @LastEditors: xujintai
 * @LastEditTime: 2021-04-18 13:51:45
 * @Description: file content
 * @FilePath: \music-admin\src\views\register.vue
-->
<template>
  <div class="login">
    <div class="register-title">音乐点播系统--管理员注册</div>
    <div class="form-box">
      <el-form
        class="register-form"
        label-position="top"
        size="small"
        :inline-message="inlinemessage"
        :model="registerForm"
        :rules="registerRule"
        ref="registerForm"
        label-width="100px"
      >
        <el-form-item label="用户名" prop="username">
          <el-input type="text" v-model="registerForm.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电子邮箱" prop="email">
          <el-input type="text" v-model="registerForm.email" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input type="password" v-model="registerForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="确认密码" prop="checkPass">
          <el-input type="password" v-model="registerForm.checkPass" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="图片验证码" prop="inputCaptcha">
          <div class="yzm">
            <el-input style="width:150px;" v-model="inputCaptcha" placeholder="请输入验证码"></el-input>
            <img
              width="100"
              style="background:#EEE9E9;margin-left:30px;"
              ref="captcha"
              height="65"
              src="http://localhost:8633/api/safecode"
              @click="refreshCaptcha"
            />
          </div>
        </el-form-item>
        <el-form-item>
          <el-button class="login-btn" type="primary" @click="submitForm('registerForm')">点击注册</el-button>
          <div>
            <el-button class="login-btn" type="success" @click="$router.push({ name: 'login' })">去登录</el-button>
          </div>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
import wsmLoading from "@/plugins/wsmLoading";
import jwt_decode from "jwt-decode";
export default {
  name: "login",
  data() {
    const validateCaptcha = (rule, value, callback) => {
      if (!this.inputCaptcha.trim().length) {
        callback(new Error("请输入验证码~"));
      } else {
        callback();
      }
    };
    const validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
      } else {
        if (this.registerForm.checkPass !== "") {
          this.$refs.registerForm.validateField("checkPass");
        } else if (value.length < 2) {
          callback(new Error("密码长度不能少于2位!"));
        }
        callback();
      }
    };
    const validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.registerForm.password) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
    const validateUsername = (rule, value, callback) => {
      if (!value.trim()) {
        return callback(new Error("用户名不能为空"));
      }
      callback();
    };

    const validateEmail = (rule, value, callback) => {
      if (value.length === "") {
        callback(new Error("请输入电子邮箱"));
      } else {
        const rules = /^([A-z0-9]{6,18})(\w|\-)+@[A-z0-9]+\.([A-z]{2,3})$/.test(
          value
        );
        if (!rules) {
          callback(new Error("请输入正确的电子邮箱"));
        } else {
          callback();
        }
      }
    };
    return {
      inline: true,
      inlinemessage: false,
      registerForm: {
        password: "",
        checkPass: "",
        username: "",
        email: "",
      },
      registerRule: {
        email: [{ validator: validateEmail, trigger: "blur" }],
        username: [
          { validator: validateUsername, trigger: "blur", required: true },
        ],
        password: [
          { validator: validatePass, trigger: "blur", required: true },
        ],
        checkPass: [
          { validator: validatePass2, trigger: "blur", required: true },
        ],
        inputCaptcha: [
          { required: true, validator: validateCaptcha, trigger: "blur" },
        ],
      },
      inputCaptcha: "",
    };
  },
  methods: {
    // 注册
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          wsmLoading.start("正在注册,请稍候...");
          setTimeout(() => {
            if (this.inputCaptcha.toLowerCase() == this.getCookie("captcha")) {
              this.$axios
                .post(
                  "http://localhost:8633/api/admin/account/register",
                  this.registerForm
                )
                .then((res) => {
                  if (res.status === 200) {
                    const { data } = res;
                    const { result } = data;
                    this.$Message.success(`${result}`);
                    wsmLoading.end();
                  }
                })
                .catch((error) => {
                  wsmLoading.end();
                  console.error(error.response);
                  this.refreshCaptcha();
                });
            } else {
              wsmLoading.end();
              this.$Modal.confirm({
                title: "错误",
                content: "你输入的验证码有误,请重新输入验证码~",
                type: "error",
                onOk: () => {
                  // this.refreshCaptcha();
                },
              });
            }
          }, 900);
        }
      });
    },
    // 获取验证码cookie
    getCookie(cname) {
      var name = cname + "=";
      var ca = document.cookie.split(";");
      for (var i = 0; i < ca.length; i++) {
        var c = ca[i].trim();
        if (c.indexOf(name) == 0) return c.substring(name.length, c.length);
      }
      return "";
    },
    // 刷新验证码
    refreshCaptcha() {
      this.$refs.captcha.src =
        "http://localhost:8633/api/safecode?d=" + Math.random();
    },
  },
};
</script>
<style lang="less" scoped>
.login {
  width: 100%;
  height: 100%;
  cursor: default;
  padding: 40px 0px;
  color: red;
  background-image: url(../assets/image/login-bg.jpg);
  background-size: 100% 100%;
  display: flex;
  flex-direction: column;

  .register-title {
    width: 100%;
    height: 150px;
    line-height: 150px;
    text-align: center;
    font-size: 40px;
    color: red;
    font-family: "Times New Roman";
  }

  // 表单
  .form-box {
    width: 100%;
    // min-width: 500px;

    .register-form {
      width: 300px;
      margin: 0px auto;
      background-color: rgb(255, 255, 255);
      border: 5px solid #cdcdcd;
      padding: 10px 15px;
      border-radius: 5px;

      .login-btn {
        width: 100%;
        background: linear-gradient(
          to bottom,
          rgb(25, 176, 223),
          rgb(25, 176, 223)
        );
        font-weight: 600;
      }
      .login-btn:hover {
        background-color: rgb(25, 176, 223);
      }

      // 验证码区域
      .yzm {
        display: flex;
        align-content: center;
        input {
          width: 160px;
          height: 32px;
          outline: none;
          border: 1px solid #eee;
          padding: 2px 15px;
          border-radius: 5px;
          font-size: 13px;
        }
        ::-webkit-input-placeholder {
          color: #bbb;
        }
        img:hover {
          cursor: pointer;
        }
      }
    }
  }
}
</style>

