<template>
  <div class="mv-container">
    <div class="mv-wrap">
      <h3 class="title">mv详情</h3>
      <!-- mv -->
      <div class="video-wrap">
        <video controls :src="mvurl"></video>
      </div>
      <!-- mv信息 -->
      <div class="info-wrap">
        <div class="singer-info">
          <div class="avatar-wrap">
            <img :src="mvdetails.cover" alt />
          </div>
          <span class="name">{{mvdetails.artistName}}</span>
        </div>
        <div class="mv-info">
          <h2 class="title">{{mvdetails.briefDesc}}</h2>
          <span class="date">发布：{{mvdetails.publishTime}}</span>
          <span class="number">播放：{{mvdetails.playCount}}次</span>
          <p class="desc">{{mvdetails.desc}}</p>
        </div>
      </div>
      <!-- 精彩评论 -->
      <div class="comment-wrap">
        <p class="title">
          精彩评论
          <span class="number">({{hotComments.length}})</span>
        </p>
        <div class="comments-wrap">
          <div class="item" v-for="(item,index) in hotComments" :key="index">
            <div class="icon-wrap">
              <!-- 头像 -->
              <img :src="item.user.avatarUrl" alt />
            </div>
            <div class="content-wrap">
              <div class="content">
                <!-- 昵称 -->
                <span class="name">{{item.user.nickname}}：</span>
                <span class="comment">{{item.content}}</span>
              </div>
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
          <span class="number">({{totalNum}})</span>
        </p>
        <div class="comments-wrap">
          <div class="item" v-for="(item,index) in comments" :key="index">
            <div class="icon-wrap">
              <img :src="item.user.avatarUrl" alt />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">{{item.user.nickname}}：</span>
                <span class="comment">{{item.content}}</span>
              </div>
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
        :limit="limit"
      ></el-pagination>
    </div>
    <div class="mv-recommend">
      <h3 class="title">相关推荐</h3>
      <div class="mvs">
        <div class="items">
          <div class="item" v-for="(item,index) in recommendMvList" :key="index">
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
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "mv",
  data() {
    return {
      // 总条数
      total: 0,
      totalNum: 0,
      // 页码
      page: 1,
      // 页容量
      limit: 10,
      //存储mv播放链接
      mvurl: "",
      mvid: 0,
      //推荐mv列表
      recommendMvList: [],
      mvdetails: "",
      //热门评论
      hotComments: [],
      //最新评论
      comments: [],
      limit: 20,
    };
  },
  created() {
    //获取评论信息
    axios({
      method: "get",
      url: "https://autumnfish.cn/comment/mv",
      params: {
        id: this.$route.query.q,
        limit: this.limit,
        offset: (this.page - 1) * 5,
      },
    }).then((res) => {
      this.comments = res.data.comments;
      this.hotComments = res.data.hotComments;
      this.totalNum = res.data.total;
      this.total = res.data.total;
      console.log(res.data);
    });
    //获取mv播放的链接
    axios({
      method: "get",
      url: "https://autumnfish.cn/mv/url",
      params: {
        id: this.$route.query.q,
      },
    }).then((res) => {
      this.mvurl = res.data.data.url;
      this.mvid = res.data.data.id;
      // console.log(this.mvid);
      //  console.log(res);
    });
    //获取相关推荐的mv
    axios({
      mehtod: "get",
      url: "https://autumnfish.cn/simi/mv",
      params: {
        mvid: this.$route.query.q,
      },
    }).then((res) => {
      this.recommendMvList = res.data.mvs;
      //对播放数量进行处理，当大于一万时，用万为单位
      this.recommendMvList.forEach(function (val) {
        if (val.playCount > 10000) {
          val.playCount = parseInt(val.playCount / 10000) + "万";
        }
        // 把mv的时长进行格式化
        let min = parseInt(val.duration / 1000 / 60);
        if (min < 10) {
          min = "0" + min;
        }
        let sec = (val.duration / 1000) % 60;
        if (sec < 10) {
          sec = "0" + sec;
        }
        val.duration = min + ":" + sec;
      });
      // console.log(res.data.mvs);
    });
    //获取mv的信息
    axios({
      method: "get",
      url: "https://autumnfish.cn/mv/detail",
      params: {
        mvid: this.$route.query.q,
      },
    }).then((res) => {
      this.mvdetails = res.data.data;
      if (res.data.data.playCount > 10000) {
        res.data.data.playCount =
          parseInt(res.data.data.playCount / 10000) + "万";
      }
      console.log(this.mvdetails);
      // console.log(res);
    });
  },
  methods: {
    handleCurrentChange(val) {
      this.page = val;
      //获取评论信息
      axios({
        method: "get",
        url: "https://autumnfish.cn/comment/mv",
        params: {
          id: this.$route.query.q,
          limit: this.limit,
          offset: (this.page - 1) * 5,
        },
      }).then((res) => {
        this.comments = res.data.comments;
        // this.hotComments = res.data.hotComments;
        this.totalNum = res.data.total;
        console.log(res.data);
      });
      console.log(`当前页: ${val}`);
    },
  },
};
</script>

<style></style>
