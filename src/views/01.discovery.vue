<template>
  <div class="discovery-container">
    <!-- 轮播图 -->
    <el-carousel class :interval="4000" type="card">
      <el-carousel-item v-for="(item,index) in bannersImg" :key="index">
        <img :src="item.imageUrl" alt />
      </el-carousel-item>
    </el-carousel>
    <!-- 推荐歌单 -->
    <div class="recommend">
      <h3 class="title">推荐歌单</h3>
      <div class="items">
        <div class="item" v-for="(item,index) in list" :key="index">
          <div class="img-wrap">
            <div class="desc-wrap">
              <span class="desc">{{item.copywriter}}</span>
            </div>
            <img :src="item.picUrl" alt />
            <span class="iconfont icon-play"></span>
          </div>
          <p class="name">{{item.name}}</p>
        </div>
      </div>
    </div>
    <!-- 最新音乐 -->
    <div class="news">
      <h3 class="title">最新音乐</h3>
      <div class="items">
        <div class="item" v-for="(item,index) in songs" :key="index">
          <!-- 封面 -->
          <div class="img-wrap">
            <img :src="item.picUrl" alt />
            <span class="iconfont icon-play" @click="playMusic(item.id)"></span>
          </div>
          <div class="song-wrap">
            <!-- 歌曲名称 -->
            <div class="song-name">{{item.name}}</div>
            <!-- 歌手名字 -->
            <div class="singer">{{item.song.artists[0].name}}</div>
          </div>
        </div>
      </div>
    </div>
    <!-- 推荐MV -->
    <div class="mvs">
      <h3 class="title">推荐MV</h3>
      <div class="items">
        <div class="item" v-for="(item,index) in mvs" :key="index">
          <div class="img-wrap">
            <img :src="item.picUrl" alt />
            <span class="iconfont icon-play"></span>
            <div class="num-wrap">
              <div class="iconfont icon-play"></div>
              <div class="num">{{item.playCount}}</div>
            </div>
          </div>
          <div class="info-wrap">
            <div class="name">{{item.name}}</div>
            <div class="singer">{{item.artists[0].name}}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "discovery",
  data() {
    return {
      bannersImg: [],
      //推荐音乐
      list: [],
      //最新音乐
      songs: [],
      //推荐mv
      mvs: [],
    };
  },
  created() {
    //轮播图
    axios.get("https://autumnfish.cn/banner").then(
      (response) => {
        // console.log(response.data.banners);

        this.bannersImg = response.data.banners;
      },
      function (err) {}
    );
    //推荐歌单
    axios({
      method: "get",
      url: "https://autumnfish.cn/personalized",
      params: {
        limit: 10,
      },
    }).then(
      (response) => {
        //console.log(response.data.result);
        this.list = response.data.result;
      },
      function (error) {}
    );
    //最新音乐
    axios({
      method: "get",
      url: "https://autumnfish.cn/personalized/newsong",
      params: "",
    }).then(
      (response) => {
        //console.log(response.data.result);
        this.songs = response.data.result;
      },
      function (error) {}
    );
    axios({
      method: "get",
      url: "https://autumnfish.cn/personalized/mv",
      params: "",
    }).then((response) => {
      console.log(response.data.result);
      this.mvs = response.data.result;
    });
    //播放音乐
  },
  methods: {
    playMusic(id) {
      console.log(id);
      axios({
        method: "get",
        url: "https://autumnfish.cn/song/url",
        params: {
          id: id,
        },
      }).then(
        (response) => {
          console.log(response.data.data[0].url);
          let url = response.data.data[0].url;
          //子组件向父组件传参
          this.$parent.musicUrl = url;
          console.log(this.$parent.musicUrl);
        },
        function (error) {}
      );
    },
  },
};
</script>

<style >
</style>
