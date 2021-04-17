<!--
 * @Author: xujintai
 * @Date: 2021-04-15 21:37:23
 * @LastEditors: xujintai
 * @LastEditTime: 2021-04-17 22:01:32
 * @Description: file content
 * @FilePath: \music-admin\src\views\manage\ManageRemarksDetail.vue
-->
<template>
  <div id="blog-detail">
    <div class="musicInfo">
      <header>
        <h2>欢迎来到音乐评论区！</h2>
        <img :src="musicPoster" style="{height:400px;width:400px;margin-right:10px;}" alt />
      </header>
      <footer>
        <span>歌手:{{artist}}</span>
        <span>播放次数:{{playcount}}</span>
        <span>歌曲名称:{{songName}}</span>
      </footer>
    </div>
    <div class="remark">
      <!-- <div class="addremark">
        <header>
          <h2>发布评论</h2>
          <img src="@/assets/imgs/评论头像.jpg" alt style="{height:50px;width:50px;margin-right:10px;}" />
          <el-input
            type="textarea"
            :autosize="{ minRows: 2, maxRows: 4}"
            placeholder="请输入内容"
            v-model="remarks"
          ></el-input>
        </header>
        <footer>
          <el-button type="primary" @click="publishRemarks()">评论</el-button>
        </footer>
      </div>-->
      <h2>
        评论信息区
        <span>评论信息条数：{{remarksArray.length}}</span>
      </h2>
      <div class="remarkInfo" v-if="remarksArray.length>0">
        <div class="remarkInfoItem" v-for="item in remarksArray" :key="item._id">
          <header>
            <span>用户名：{{item.username}}</span>
            <span style="margin-left:55px">评论信息:{{item.remarks}}</span>
            <el-button type="danger" style="margin-left:25px" @click="removeRemarks(item._id)">删除</el-button>
          </header>
          <footer>
            <span>评论时间:{{item.date}}</span>
          </footer>
        </div>
      </div>
      <div v-else>
        <h3>暂无评论</h3>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      remarks: "",
      musicid: "",
      songName: "",
      artist: "",
      poster: "",
      playcount: "",
      remarksArray: [],
    };
  },
  created() {
    this.musicid = this.$route.params.id;
    this.songName = this.$route.params.songName;
    this.artist = this.$route.params.artist;
    this.poster = this.$route.params.poster;
    this.playcount = this.$route.params.playcount;
    this.getRemarks();
  },
  computed: {
    musicPoster() {
      return "http://localhost:8633/api/music/poster?img=" + this.poster;
    },
  },
  methods: {
    //加载评论信息
    getRemarks() {
      this.$axios
        .get(
          `http://localhost:8633/api/user/query/remarks?musicid=${this.musicid}`
        )
        .then((res) => {
          this.remarksArray = res.data;
        })
        .catch((err) => {
          console.log(err);
          this.$message.error("加载评论信息失败");
        });
    },
    //发布评论信息
    publishRemarks() {
      const flag = this.remarks.trim();
      if (flag) {
        console.log("发布");
        const { username, mobile } = JSON.parse(
          localStorage.getItem("userInfo")
        );
        this.$axios
          .post("http://localhost:8633/api/user/remark", {
            musicid: this.musicid,
            remarks: this.remarks,
            username,
            mobile,
            counts: 0,
            date: new Date(),
          })
          .then((res) => {
            //刷新评论页面
            this.getRemarks();
            this.$message.success("发布评论成功");
          })
          .catch((res) => {
            this.$message.success("发布评论失败");
          });
      } else {
        this.$message.warning("发布内容不能为空..");
      }
    },
    //移除用户评论信息
    removeRemarks(remarkid) {
      this.$confirm("确定删除该评论信息吗？", "删除评论信息提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          console.log("删除成功");
          this.$axios
            .post("http://localhost:8633/api/admin/remark/del", {
              remark_id: remarkid,
            })
            .then((res) => {
              //刷新评论数据
              this.getRemarks();
              this.$message({
                message: "评论删除成功",
                type: "success",
              });
            });
        })
        .catch(() => {});
    },
  },
};
</script>

<style scoped lang="less">
#blog-detail {
  height: 100vh;
  background-color: #fdf6ec;
  .musicInfo {
    width: 65vw;
    margin: 0;
    margin: auto;
    background-color: rgb(255, 252, 252);
    header {
      h2 {
        padding: 20px;
      }
    }
    footer {
      display: flex;
      justify-content: space-around;
      span {
        margin: 20px 30px;
      }
    }
  }
  .remark {
    width: 65vw;
    margin: 0;
    margin: auto;
    background-color: rgb(255, 252, 252);

    .addremark {
      width: 50vw;
      header {
        margin: 25px 0;
        h2 {
          padding: 20px;
        }
        .el-textarea {
          width: 35vw;
          border: 2px;
        }
      }

      .el-button {
        margin-left: 550px;
      }
    }

    .remarkInfo {
      width: 50vw;
      text-align: left;
      .remarkInfoItem {
        margin: 30px 0;
        margin-left: 150px;
      }
    }
  }
}

textarea.el-textarea__inner {
  height: 20vh !important;
}
</style>