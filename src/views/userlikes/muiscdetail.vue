<!--
 * @Author: xujintai
 * @Date: 2021-04-18 18:30:48
 * @LastEditors: xujintai
 * @LastEditTime: 2021-04-18 19:10:39
 * @Description: file content
 * @FilePath: \music-admin\src\views\userlikes\muiscdetail.vue
-->
<template>
  <div id="musicdetail">
    <h1>欢迎来到音乐详情页面</h1>
    <el-table :data="musiscInfo" border style="width: 90%">
      <el-table-column prop="_id" label="音乐id"></el-table-column>
      <el-table-column prop="songName" label="音乐名称"></el-table-column>
      <el-table-column prop="artist" label="歌手"></el-table-column>
      <el-table-column align="center" label="音乐封面">
        <template slot-scope="scope">
          <img
            :src="'http://localhost:8633/api/music/poster?img=' + scope.row.poster"
            style="width:35px;height:35px;"
          />
        </template>
      </el-table-column>
      <el-table-column prop="style" label="风格"></el-table-column>
      <el-table-column prop="playcount" label="播放次数"></el-table-column>
      <el-table-column prop="date" label="上架日期"></el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      musicid: "",
      musiscInfo: [],
    };
  },
  created() {
    this.musicid = this.$route.params.id;
    this.getMusic();
  },
  methods: {
    //获取指定id的音乐
    async getMusic() {
      const res = await this.$axios.get(
        `http://localhost:8633/api/music/queryid?id=${this.musicid}`
      );
      if (res.status === 200) {
        console.log(res.data.result);
        return (this.musiscInfo = res.data.result);
      }
      this.$message.error("服务器错误,获取音乐信息数据失败！");
    },
  },
};
</script>

<style lang="less" scoped>
#musicdetail {
  text-align: center;
  .el-table {
    margin: 0;
    margin: auto;
    margin-top: 20px;
  }
}
</style>