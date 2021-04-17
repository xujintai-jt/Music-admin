<!--
 * @Author: xujintai
 * @Date: 2021-04-17 21:19:58
 * @LastEditors: xujintai
 * @LastEditTime: 2021-04-17 22:36:04
 * @Description: file content
 * @FilePath: \music-admin\src\views\manage\ManageUser.vue
-->
<!--
 * @Author: xujintai
 * @Date: 2021-04-15 14:42:32
 * @LastEditors: xujintai
 * @LastEditTime: 2021-04-17 15:08:20
 * @Description: file content
 * @FilePath: \music-user\src\components\user\UserInfo.vue
-->
<template>
  <div id="user-info">
    <h1>欢迎来到您的个人信息页面</h1>
    <!-- 用户信息 -->
    <div style="width:100%;background-color:#f40;">
      <el-table :data="userInfos" class="song-table" border>
        <el-table-column label="序号" type="index" align="center"></el-table-column>
        <el-table-column label="用户名" prop="username" align="center"></el-table-column>
        <el-table-column label="密码" prop="password" align="center"></el-table-column>
        <el-table-column label="手机号" prop="mobile" align="center"></el-table-column>
        <el-table-column label="年龄" prop="age" align="center"></el-table-column>
        <el-table-column label="性别" align="center">
          <template slot-scope="scope">
            <span>{{Number(scope.row.sex)===1?"男":"女"}}</span>
          </template>
        </el-table-column>
        <el-table-column label="编辑" align="center">
          <template slot-scope="scope">
            <el-button icon="el-icon-edit" @click="collectionMusic(scope.row)"></el-button>
          </template>
        </el-table-column>
        <el-table-column label="删除" align="center">
          <template slot-scope="scope">
            <el-button icon="el-icon-delete" @click="collectionMusic(scope.row)"></el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <!-- 编辑用户信息 -->
    <el-dialog
      :title="editDialog.title"
      :visible.sync="editDialog.show"
      :close-on-click-modal="false"
      :close-on-press-escape="false"
      :modal-append-to-body="false"
    >
      <el-form
        :model="ruleForm"
        status-icon
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="用户名" prop="username">
          <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="手机号">
          <span>{{userInfos.mobile}}</span>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input type="password" v-model="ruleForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="确认密码" prop="checkPass">
          <el-input type="password" v-model="ruleForm.checkPass" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="年龄" prop="age">
          <el-input v-model.number="ruleForm.age"></el-input>
        </el-form-item>
        <el-form-item label="性别">
          <el-radio v-model="ruleForm.sex" label="1">男</el-radio>
          <el-radio v-model="ruleForm.sex" label="2">女</el-radio>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    const validateUsername = (rule, value, callback) => {
      if (!value.trim()) {
        return callback(new Error("用户名不能为空"));
      }
      callback();
    };
    const checkAge = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("年龄不能为空"));
      }
      setTimeout(() => {
        if (!Number.isInteger(value)) {
          callback(new Error("请输入数字值"));
        } else {
          if (value < 12) {
            callback(new Error("必须年满12岁"));
          } else {
            callback();
          }
        }
      }, 100);
    };
    const validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
      } else {
        if (this.ruleForm.checkPass !== "") {
          this.$refs.ruleForm.validateField("checkPass");
        } else if (value.length < 2) {
          callback(new Error("密码长度不能少于2位!"));
        }
        callback();
      }
    };
    const validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.ruleForm.password) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
    const validateMobile = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入手机号"));
      } else {
        if (!/^1[3456789]\d{9}$/.test(value)) {
          callback(new Error("请输入正确的手机号"));
        } else {
          callback();
        }
      }
    };
    return {
      ruleForm: {
        password: "",
        checkPass: "",
        age: "",
        mobile: "",
        sex: "",
        username: "",
      },
      userInfos: {
        // password: "",
        // age: "",
        // mobile: "",
        // sex: "",
        // username: "",
      },
      editDialog: {
        title: "编辑用户信息",
        show: false,
      },
      themeColor: "浪漫",
      rules: {
        username: [
          { validator: validateUsername, trigger: "blur", required: true },
        ],
        password: [
          { validator: validatePass, trigger: "blur", required: true },
        ],
        checkPass: [
          { validator: validatePass2, trigger: "blur", required: true },
        ],
        age: [{ validator: checkAge, trigger: "blur", required: true }],
        mobile: [
          { validator: validateMobile, trigger: "blur", required: true },
        ],
      },
    };
  },
  created() {
    //获取用户信息
    // const mobile = JSON.parse(localStorage.getItem("userInfo")).mobile;
    this.getUserInfo();
  },
  methods: {
    //获取用户信息
    getUserInfo(mobile) {
      this.$axios
        .get(`http://localhost:8633/api/user/query/all`)
        .then((res) => {
          const { data } = res;
          console.log(data);
          if (data.status !== 200) {
            return this.$message({
              message: "暂无用户注册信息",
              type: "warning",
            });
          }
          this.userInfos = data.result;
        })
        .catch((err) => console.log(err));
    },
    submitForm(formName) {
      console.log(11111111);
      this.$refs[formName].validate(async (valid) => {
        if (valid) {
          this.ruleForm.mobile = this.userInfos.mobile;
          //请求头"Content-Type"设置为"application/x-www-form-urlencoded"
          const res = await this.$axios.post(
            "http://localhost:8633/api/user/edit",
            this.ruleForm
          );
          const { data, status } = res;
          if (status === 200) {
            //刷新表单数据,隐藏表单;刷新页面上的用户数据
            this.resetForm("ruleForm");
            this.editDialog.show = false;
            this.getUserInfo(this.userInfos.mobile);
            return this.$message({
              message: data.result,
              type: "success",
            });
          }

          this.$message({
            message: data,
            type: "error",
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
    currentTheme() {
      //可以本地化存储themeColor
      console.log(this.themeColor);
      console.log(this.$root);
    },
  },
};
</script>

<style lang="less" scoped>
#user-info {
  height: 100vh;
  background-color: #e9fafd;
  .userInfo {
    display: flex;
    justify-content: space-around;
    align-items: center;
    height: 10vh;
    background-color: #f9fbf8;
    span {
      margin-right: 10px;
    }
  }
}
</style>