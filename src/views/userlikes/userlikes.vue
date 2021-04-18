<!--
 * @Author: xujintai
 * @Date: 2021-04-17 21:19:58
 * @LastEditors: xujintai
 * @LastEditTime: 2021-04-18 19:11:21
 * @Description: file content
 * @FilePath: \music-admin\src\views\userlikes\userlikes.vue
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
  <div id="user-likes">
    <h1>欢迎来到用户收藏信息管理页面</h1>
    <!-- 用户信息 -->
    <div style="margin-top:40px;width:100%;background-color:#f40;">
      <el-table :data="userInfos" class="song-table" border>
        <el-table-column label="序号" type="index" align="center"></el-table-column>
        <el-table-column label="用户id" prop="_id" align="center"></el-table-column>
        <el-table-column label="用户名" prop="username" align="center"></el-table-column>
        <el-table-column label="手机号" prop="mobile" align="center"></el-table-column>
        <el-table-column label="年龄" prop="age" align="center"></el-table-column>
        <el-table-column label="性别" align="center">
          <template slot-scope="scope">
            <span>{{Number(scope.row.sex)===1?"男":"女"}}</span>
          </template>
        </el-table-column>
        <el-table-column label="用户收藏音乐详情" align="center">
          <template slot-scope="scope">
            <div @click="toUserlikesDetail(scope.row)">
              <span style="color:red">{{scope.row.username}}</span>
              <span style="color:blue">的音乐收藏详情</span>
            </div>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <footer style="margin-top:40px">
      <h1>去查看所有用户收藏的音乐系数</h1>
      <el-button style="margin-top:30px" type="primary" @click="toAllUserLikes()">前往所有用户音乐收藏界面</el-button>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userInfos: [],
    };
  },
  created() {
    this.getUserInfo();
  },
  methods: {
    //获取用户信息
    getUserInfo() {
      this.$axios
        .get(`http://localhost:8633/api/user/query/all`)
        .then((res) => {
          const { data } = res;
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
    //跳转用户收藏详情页面
    toUserlikesDetail(row) {
      const id = row._id;
      const { username } = row;
      this.$router.push({
        name: "manageuserlikesdetails",
        params: { id, username },
      });
    },
    //前往所有用户音乐收藏界面
    toAllUserLikes() {
      this.$router.push({ name: "managealluserlikes" });
    },
  },
};
</script>

<style lang="less" scoped>
#user-likes {
  text-align: center;
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
  .el-table {
    margin: 0;
    margin: auto;
    margin-top: 20px;
  }
}
</style>