<template>
  <div class="playlists-container">
    <!-- 同步 -->
    <div class="top-card">
      <div class="icon-wrap">
        <img :src="topList.coverImgUrl" alt />
      </div>
      <div class="content-wrap">
        <div class="tag">精品歌单</div>
        <div class="title">{{topList.name}}</div>
        <div class="info">{{topList.description}}</div>
      </div>
      <img src="../assets/listCover.jpg" alt class="bg" />
      <div class="bg-mask"></div>
    </div>
    <div class="tab-container">
      <!-- tab栏 顶部 -->
      <div class="tab-bar">
        <span class="item" @click="tag='全部'" :class="{active:tag=='全部'}">全部</span>
        <span class="item" @click="tag='欧美'" :class="{active:tag=='欧美'}">欧美</span>
        <span class="item" @click="tag='华语'" :class="{active:tag=='华语'}">华语</span>
        <span class="item" @click="tag='流行'" :class="{active:tag=='流行'}">流行</span>
        <span class="item" @click="tag='说唱'" :class="{active:tag=='说唱'}">说唱</span>
        <span class="item" @click="tag='摇滚'" :class="{active:tag=='摇滚'}">摇滚</span>
        <span class="item" @click="tag='民谣'" :class="{active:tag=='民谣'}">民谣</span>
        <span class="item" @click="tag='电子'" :class="{active:tag=='电子'}">电子</span>
        <span class="item" @click="tag='轻音乐'" :class="{active:tag=='轻音乐'}">轻音乐</span>
        <span class="item" @click="tag='影视原声'" :class="{active:tag=='影视原声'}">影视原声</span>
        <span class="item" @click="tag='ACG'" :class="{active:tag=='ACG'}">ACG</span>
        <span class="item" @click="tag='怀旧'" :class="{active:tag=='怀旧'}">怀旧</span>
        <span class="item" @click="tag='治愈'" :class="{active:tag=='治愈'}">治愈</span>
      </div>
      <!-- tab的内容区域 -->
      <div class="tab-content">
        <div class="items">
          <div class="item" v-for="(item,index) in list" :key="index">
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
      </div>
    </div>
    <!-- 分页器
      total总页数
      current-page当前页
      page-size每页多少条数据
    -->
    <el-pagination
      @current-change="handleCurrentChange"
      background
      layout="prev, pager, next"
      :total="total"
      :current-page="page"
      :page-size="10"
    ></el-pagination>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "recommend",

  data() {
    return {
      // 总条数
      total: 0,
      // 页码
      page: 1,
      //精品歌单
      topList: {},
      //歌单列表
      list: [],
      //导航切换歌单类型
      tag: "欧美",
    };
  },
  //设置监听器，当tag标签发生改变时调用axios请求
  watch: {
    tag() {
      // 切换tag时,将分页置为1
      this.page = 1;
      this.topData();
      this.listData();
    },
  },
  created() {
    //精品歌单
    this.topData();
    this.listData();
  },
  methods: {
    topData() {
      axios({
        method: "get",
        url: "https://autumnfish.cn/top/playlist/highquality",
        params: {
          limit: 1,
          cat: this.tag,
        },
      }).then((response) => {
        console.log(response.data.playlists[0]);
        this.topList = response.data.playlists[0];
      });
    },
    listData() {
      axios({
        method: "get",
        url: "https://autumnfish.cn/top/playlist",
        params: {
          limit: 10,
          offset: (this.page - 1) * 10,
          cat: this.tag,
        },
      }).then((response) => {
        console.log(response.data.total);
        this.total = response.data.total;
        console.log(response.data.playlists);
        this.list = response.data.playlists;
      });
    },
    handleCurrentChange(val) {
      // 将当前页码的值给page
      this.page = val;
      //点击分页重新请求ajax获取新的数据
      this.listData();
      console.log(`当前页: ${val}`);
    },
  },
};
</script>

<style >
</style>
