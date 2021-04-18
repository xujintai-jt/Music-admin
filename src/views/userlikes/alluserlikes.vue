<!--
 * @Author: xujintai
 * @Date: 2021-04-18 16:49:22
 * @LastEditors: xujintai
 * @LastEditTime: 2021-04-18 18:56:41
 * @Description: file content
 * @FilePath: \music-admin\src\views\userlikes\alluserlikes.vue
-->
<template>
  <div id="all-userlikes">
    <h1>所有用户音乐收藏界面</h1>
    <div v-for="(item,index) in UserLikemusicsCount" :key="index">
      <div class="musicLike-Count">
        <span>音乐ID:</span>
        <el-button @click="toMusicDetail(index)" type="primary" plain>{{index}}</el-button>
        <span>所有用户收藏次数:</span>
        <span>{{item}}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      UserLikemusicsCount: "",
    };
  },
  created() {
    this.getUserLike();
  },
  methods: {
    //获取用户收藏音乐
    getUserLike() {
      this.$axios
        .get(`http://localhost:8633/api/music/alluserlike`)
        .then((res) => {
          const { result } = res.data;
          // 将数据进行数量累加处理
          this.UserLikemusicsCount = this.countHandle(result);
          // console.log(this.UserLikemusicsCount);
        })
        .catch((err) => console.log(err));
    },
    //处理对象数据，进行累加
    countHandle(targetArr) {
      const obj = {};
      targetArr.forEach((item) => {
        const { musicid } = item;
        if (obj[musicid]) {
          obj[musicid]++;
        } else {
          obj[musicid] = 1;
        }
      });
      return obj;
    },
    //跳转到musicDetail页面
    toMusicDetail(id) {
      this.$router.push({ name: "managemusicdetail", params: { id } });
    },
  },
};
</script>

<style lang="less" scoped>
#all-userlikes {
  font-size: 20px;
  text-align: center;
  .musicLike-Count {
    display: flex;
    justify-content: space-around;
    margin-top: 20px;
  }
}
</style>