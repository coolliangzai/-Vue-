<template>
  <div class="playlist-container">
    <div class="top-wrap">
      <div class="img-wrap">
        <img :src="playList.coverImgUrl" alt />
      </div>
      <div class="info-wrap">
        <!-- 名字 -->
        <p class="title">{{playList.name}}</p>
        <div class="author-wrap">
          <img class="avatar" :src="playList.creator.avatarUrl" alt />
          <span class="name">{{playList.creator.nickname}}</span>
          <span class="time">{{playList.createTime}} 创建</span>
        </div>
        <div class="play-wrap">
          <span class="iconfont icon-circle-play"></span>
          <span class="text">播放全部</span>
        </div>
        <div class="tag-wrap">
          <span class="title">标签:</span>
          <ul>
            <li v-for="(item,index) in playList.tags" :key="index">{{item}}</li>
          </ul>
        </div>
        <div class="desc-wrap" v-if="playList.description!=''">
          <span class="title">简介:</span>
          <span class="desc">{{playList.description}}</span>
        </div>
      </div>
    </div>
    <el-tabs v-model="activeIndex">
      <el-tab-pane label="歌曲列表" name="1">
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
            <tr class="el-table__row" v-for="(item,index) in playList.tracks" :key="index">
              <td>{{index+1}}</td>
              <td>
                <div class="img-wrap">
                  <img :src="item.al.picUrl" alt />
                  <span class="iconfont icon-play"></span>
                </div>
              </td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap">
                    <span>{{item.name}}</span>
                    <span class="iconfont icon-mv"></span>
                  </div>
                  <span>{{item.alia[0]}}</span>
                </div>
              </td>
              <td>{{item.ar[0].name}}</td>
              <td>{{item.al.name}}</td>
              <td>06:03</td>
            </tr>
          </tbody>
        </table>
      </el-tab-pane>
      <el-tab-pane :label="'评论('+total+')'" name="2">
        <!-- 精彩评论 -->
        <div class="comment-wrap">
          <p class="title">
            精彩评论
            <span class="number">({{this.hotNum}})</span>
          </p>
          <div class="comments-wrap">
            <div class="item" v-for="(item,index) in hotComments" :key="index">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{item.user.nickname}}：</span>
                  <span class="comment">{{item.content}}</span>
                </div>
                <!-- 回复 -->
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{item.beReplied[0].user.nickname}}：</span>
                  <span class="comment">{{item.beReplied[0].content}}</span>
                </div>
                <div class="date">2020-02-12 17:26:11</div>
              </div>
            </div>
          </div>
        </div>
        <!-- 最新评论 -->
        <div class="comment-wrap">
          <p class="title">
            最新评论
            <span class="number">({{newNum}})</span>
          </p>
          <div class="comments-wrap">
            <div class="item" v-for="(item,index) in newComments" :key="index">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{item.user.nickname}}：</span>
                  <span class="comment">{{item.content}}</span>
                </div>
                <!-- 回复 -->
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{item.beReplied[0].user.nickname}}：</span>
                  <span class="comment">{{item.beReplied[0].content}}</span>
                </div>
                <div class="date">2020-02-12 17:26:11</div>
              </div>
            </div>
          </div>
        </div>
        <!-- 分页器 -->
        <el-pagination
          @current-change="handleCurrentChange"
          background
          layout="prev, pager, next"
          :total="total"
          :current-page="page"
          :page-size="5"
        ></el-pagination>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "playlist",
  data() {
    return {
      activeIndex: "1",
      // 总条数
      total: 0,
      // 页码
      page: 1,
      playList: [],
      //热门评论
      hotComments: [],
      //热门评论数量
      hotNum: 0,
      //最新评论数量
      newNum: 0,
      limit: 10,
      //最新评论
      newComments: [],
    };
  },
  // 页面初次加载时
  created() {
    //最新评论
    axios({
      method: "get",
      url: "https://autumnfish.cn/comment/playlist",
      params: {
        id: this.$route.query.q,
        limit: this.limit,
        offset: (this.page - 1) * this.limit,
      },
    }).then((res) => {
      this.newNum = res.data.total;
      this.total = res.data.total;
      this.newComments = res.data.comments;
      console.log(res.data.comments);
    });
    //获取热门评论
    axios({
      method: "get",
      url: "https://autumnfish.cn/comment/hot",
      params: {
        type: 2,
        id: this.$route.query.q,
      },
    }).then((res) => {
      this.hotNum = res.data.total;
      this.hotComments = res.data.hotComments;
      // console.log(res.data);
    });
    //获取歌单列表
    axios({
      method: "get",
      url: "https://autumnfish.cn/playlist/detail",
      params: {
        //this.$route.query.q是路由传过来的参数
        id: this.$route.query.q,
      },
    }).then((res) => {
      // console.log(res);
      this.playList = res.data.playlist;
      //console.log(res.data.playlist);
      this.playList.birthday;
    });
  },
  methods: {
    handleCurrentChange(val) {
      this.page = val;
      console.log(this.page);
      // 重新获取数据
      axios({
        method: "get",
        url: "https://autumnfish.cn/comment/playlist",
        params: {
          id: this.$route.query.q,
          limit: this.limit,
          offset: (this.page - 1) * this.limit,
        },
      }).then((res) => {
        this.newNum = res.data.total;
        this.total = res.data.total;
        this.newComments = res.data.comments;
        console.log(res.data.comments);
      });
      console.log(`当前页: ${val}`);
    },
  },
};
</script>

<style >
</style>
