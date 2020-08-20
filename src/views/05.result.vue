<template>
  <div class="result-container">
    <div class="title-wrap">
      <h2 class="title">{{$route.query.q}}</h2>
      <!-- 搜索结果总数 -->
      <span class="sub-title">{{count}}</span>
    </div>
    <el-tabs v-model="activeIndex">
      <el-tab-pane label="歌曲" name="songs">
        <table class="el-table">
          <thead>
            <th></th>
            <th>音乐标题</th>
            <th>歌手</th>
            <th>专辑</th>
            <th>时长</th>
          </thead>
          <tbody>
            <tr
              class="el-table__row"
              v-for="(item,index) in musicList"
              :key="index"
              @dblclick="playMusic(item.id)"
            >
              <td>{{index+1}}</td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap">
                    <span>{{item.name}}</span>
                    <!-- 播放mv的图标 -->
                    <span class="iconfont icon-mv" v-if="item.mvid!=0"></span>
                  </div>
                  <span v-if="item.alias.length!=0">{{item.alias[0]}}</span>
                </div>
              </td>
              <td>{{item.artists[0].name}}</td>
              <td>{{item.album.name}}</td>
              <td>{{item.duration}}</td>
            </tr>
          </tbody>
        </table>
      </el-tab-pane>
      <el-tab-pane label="歌单" name="lists">
        <div class="items">
          <div
            class="item"
            v-for="(item,index) in playList"
            :key="index"
            @click="toPlayList(item.id)"
          >
            <div class="img-wrap">
              <div class="num-wrap">
                播放量:
                <span class="num">{{item.playCount}}</span>
              </div>
              <img :src="item.coverImgUrl" alt />
              <span class="iconfont icon-play"></span>
            </div>
            <p class="name">{{item.name}}</p>
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane label="MV" name="mv">
        <div class="items mv">
          <div
            class="item"
            v-for="(item,index) in mvList"
            :key="index"
            @click="toPlayMvList(item.id)"
          >
            <div class="img-wrap">
              <img :src="item.cover" alt />
              <span class="iconfont icon-play"></span>
              <div class="num-wrap">
                <div class="iconfont icon-play"></div>
                <div class="num">{{item.playCount}}</div>
              </div>
              <span class="time">{{item.duration}}</span>
            </div>
            <div class="info-wrap">
              <div class="name">{{item.name}}</div>
              <div class="singer">{{item.artistName}}</div>
            </div>
          </div>
        </div>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "result",
  data() {
    return {
      musicList: [],
      activeIndex: "songs",
      // 搜索结果的总数
      count: 0,
      musicUrl: "",
      type: 1,
      playList: [],
      mvList: [],
    };
  },
  watch: {
    activeIndex() {
      switch (this.activeIndex) {
        case "songs":
          this.type = 1;
          // console.log(this.type);
          break;
        case "lists":
          this.type = 1000;
          //  console.log(this.type);
          break;
        case "mv":
          this.type = 1004;
          //  console.log(this.type);
          break;

        default:
          break;
      }
      if (this.type == 1) {
        //获取搜索结果
        axios({
          method: "get",
          url: "https://autumnfish.cn/search",
          params: {
            keywords: this.$route.query.q,
            //单曲
            type: this.type,
            //获取的数据量
            limit: 10,
          },
        }).then((res) => {
          this.musicList = res.data.result.songs;
          console.log(this.musicList);
          this.count = res.data.result.songCount;
          this.musicList.forEach(function (val) {
            let time = val.duration;
            let sec = parseInt((val.duration / 1000) % 60);
            if (sec < 10) {
              sec = "0" + sec;
            }
            let min = parseInt(val.duration / 1000 / 60);
            if (min < 10) {
              min = "0" + min;
            }
            val.duration = min + ":" + sec;
          });
        });
      } else if (this.type == 1000) {
        //获取搜索结果
        axios({
          method: "get",
          url: "https://autumnfish.cn/search",
          params: {
            keywords: this.$route.query.q,
            //单曲
            type: this.type,
            //获取的数据量
            limit: 10,
          },
        }).then((res) => {
          this.count = res.data.result.playlistCount;
          this.playList = res.data.result.playlists;
          //console.log(res.data.result.playlists);
          this.playList.forEach(function (val) {
            if (val.playCount > 10000) {
              val.playCount = parseInt(val.playCount / 100000) + "万";
            }
          });
        });
      } else if (this.type == 1004) {
        //获取搜索结果
        axios({
          method: "get",
          url: "https://autumnfish.cn/search",
          params: {
            keywords: this.$route.query.q,
            //单曲
            type: this.type,
            //获取的数据量
            limit: 12,
          },
        }).then((res) => {
          this.count = res.data.result.mvCount;
          // console.log(res.data.result.mvCount);
          this.mvList = res.data.result.mvs;
          console.log(res);
          this.mvList.forEach(function (val) {
            if (val.playCount > 10000) {
              val.playCount = parseInt(val.playCount / 10000) + "万";
              let time = val.duration;
              let sec = parseInt((val.duration / 1000) % 60);
              if (sec < 10) {
                sec = "0" + sec;
              }
              let min = parseInt(val.duration / 1000 / 60);
              if (min < 10) {
                min = "0" + min;
              }
              val.duration = min + ":" + sec;
            }
          });
        });
      }
    },
  },
  methods: {
    // 抽取出播放音乐的方法
    playAxios(id) {
      axios({
        method: "get",
        url: "https://autumnfish.cn/song/url",
        params: {
          id: id,
        },
      }).then((res) => {
        //获取歌曲的播放地址
        this.musicUrl = res.data.data[0].url;
      });
    },
    toPlayList(id) {
      this.$router.push("./playlist?q=" + id);
    },
    //双击播放音乐的功能
    playMusic(id) {
      this.playAxios(id);
      // 将当前组件的musicUrl传给父组件的musicUrl
      this.$parent.musicUrl = this.musicUrl;
    },
    toPlayMvList(id) {
      this.$router.push("./mv?q=" + id);
    },
  },
  created() {
    console.log(2);
    //获取搜索结果
    axios({
      method: "get",
      url: "https://autumnfish.cn/search",
      params: {
        keywords: this.$route.query.q,
        //单曲
        type: 1,
        //获取的数据量
        limit: 10,
      },
    }).then((res) => {
      this.musicList = res.data.result.songs;
      console.log(this.musicList);
      this.count = res.data.result.songCount;
      this.musicList.forEach(function (val) {
        let time = val.duration;
        let sec = parseInt((val.duration / 1000) % 60);
        if (sec < 10) {
          sec = "0" + sec;
        }
        let min = parseInt(val.duration / 1000 / 60);
        if (min < 10) {
          min = "0" + min;
        }
        val.duration = min + ":" + sec;
      });
    });
  },
};
</script>

<style >
</style>
