<template>
  <div class="songs-container">
    <div class="tab-bar">
      <span class="item active">全部</span>
      <span class="item">华语</span>
      <span class="item">欧美</span>
      <span class="item">日本</span>
      <span class="item">韩国</span>
    </div>
    <!-- 底部的table -->
    <table class="el-table playlit-table">
      <thead>
        <th></th>
        <th></th>
        <th>音乐标题</th>
        <th>歌手</th>
        <th>专辑</th>
        <th>时长</th>
      </thead>
      <tbody>
        <tr class="el-table__row" v-for="(item,index) in lists" :key="index">
          <td>{{index+1}}</td>
          <td>
            <div class="img-wrap">
              <img :src="item.album.blurPicUrl" alt />
              <span class="iconfont icon-play"></span>
            </div>
          </td>
          <td>
            <div class="song-wrap">
              <div class="name-wrap">
                <span>{{item.name}}</span>
                <span class="iconfont icon-mv"></span>
              </div>
              <span></span>
            </div>
          </td>
          <td>{{item.artists[0].name}}</td>
          <td>{{item.album.name}}</td>
          <td>{{item.duration}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
//引入axios
import axios from "axios";
export default {
  name: "songs",

  data() {
    return { lists: [] };
  },
  created() {
    axios({
      method: "get",
      url: " https://autumnfish.cn/top/song",
      params: { type: 0 },
    }).then((response) => {
      this.lists = response.data.data;
      console.log(response.data.data);
      //处理时长将毫秒转换为分钟和秒的格式
      this.lists.forEach((val) => {
        let duration = val.duration;
        //假定350750毫秒
        //转换为秒 350750/1000 350秒
        //转换为分 350/60
        //秒    350%60
        let min = parseInt(duration / 1000 / 60);
        if (min < 10) {
          min = "0" + min;
        }

        let sec = parseInt((duration / 1000) % 60);
        if (sec < 10) {
          sec = "0" + sec;
        }
        val.duration = min + ":" + sec;
      });
    });
  },
};
</script>

<style >
</style>
