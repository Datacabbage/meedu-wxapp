<template>
  <div class="video-info">
    <!-- video -->
    <div class="video-box">
      <video style="width: 100%;" v-if="playUrl && playUrl.length > 0" :src="playUrl[1].url"  controls objectFit="contain"></video>
      <div v-else class="">
        <h3>当前视频无法观看</h3>
      </div>
    </div>
    <!-- tabBar -->
    <div class="tabBar">
      <span class="tabBar-item" :class="{'tabBar-item-active': activeIndex === index}" v-for="(item, index) in tabBarList" :key="index" @click="activeIndex = index">{{item.name}}</span>
    </div>
    <!-- content -->
    <swiper :current="activeIndex" class="info-content" @change="swiperChange" :style="{'height': swiperHeight * 2 + 'rpx'}">
      <swiper-item>
        <scroll-view scroll-y :style="{'height': swiperHeight * 2 + 'rpx'}">
          <div class="introduction">
            <p class="introduction-title">
              <span class="introduction-title__name">{{videoInfo.title}}</span>
              <span class="introduction-title__num">播放次数：{{videoInfo.view_num}}</span>
            </p>
            <p class="introduction-lable">简介</p>
            <p class="introduction-description" v-html="videoInfo.short_description"></p>            
            <p class="introduction-lable">详细介绍</p>
            <p class="introduction-description" v-html="videoInfo.description"></p>
          </div>
        </scroll-view>
      </swiper-item>
      <!-- video list -->
      <swiper-item>
        <scroll-view  scroll-y :style="{'height': swiperHeight * 2 + 'rpx'}">
          <ul class="list-container">
            <li class="list-item" v-for="(item, index) in courseVideoList" :class="{ red: aa }" :key="index" @click="goVideo(item)" >
              <div class="video-icon">
                <img class="video-icon__img" src="../../assets/img/triangle-icon.png" alt="" mode="widthFix">
              </div>
              <div class="video-title">{{(index + 1) + ' . ' + item.title}}</div>
            </li>
          </ul>
        </scroll-view>
      </swiper-item>
      <!-- comments list -->
      <swiper-item>
        <scroll-view  scroll-y :style="{'height': swiperHeight * 2 + 'rpx'}">
          <ul class="comments-list" v-if="videosComments && videosComments.length > 0">
            <li class="list-item" v-for="(item, index) in videosComments" :class="{ red: aa }" :key="index" @click="goComment(item)">
              <img class="item-avatar" v-if="item.user" :src="item.user.avatar" alt="" mode="widthFix">
              <div class="item-content">
                <div class="item-content__name" v-if="item.user">{{item.user.nick_name}}</div>
                <div class="item-content__time">{{item.created_format}}</div>
                <div class="item-content__content" v-html="item.content"></div>
              </div>
            </li>
          </ul>
          <p v-else style="text-align: center;padding: 25px 0;">~暂无评价~</p>
        </scroll-view>
        <div class="btn-submit" for="" @click="goComment('', true)">留言</div>
      </swiper-item>
      <!-- operation -->
    </swiper>
  </div>
</template>

<script>
import { formatTime } from '@/utils/index'
import card from '@/components/card'

export default {
  components: {
    card
  },

  data () {
    return {
      src: '',
      activeIndex: 0,
      courseVideoList: [],
      tabBarList: [
        {name: '详情', index: 0, value: 'name'},
        {name: '列表', index: 0, value: 'name'},
        {name: '评价', index: 0, value: 'name'}
      ],
      playUrl: [
        {url: '1'},
        // {url: 'https://img.ccsc.work/test%2F3176f36b9cee7cbdef_10.mp4?OSSAccessKeyId=LTAIfNCN3kQQlBHl&Expires=1630107264&Signature=MpMLkHgtRZ5jzmye4ACSt%2BALejo%3D'}
        {url: 'http://wxsnsdy.tc.qq.com/105/20210/snsdyvideodownload?filekey=30280201010421301f0201690402534804102ca905ce620b1241b726bc41dcff44e00204012882540400&bizid=1023&hy=SH&fileparam=302c020101042530230204136ffd93020457e3c4ff02024ef202031e8d7f02030f42400204045a320a0201000400'}
      ],
      // video
      videoId: '',
      videosComments: [],
      videoInfo: {},
      swiperHeight: wx.getSystemInfoSync().windowHeight - 265
    }
  },
  computed: {
    // videoId () {
    //   return this.$mp.query.id
    // }
    // swiperHeight () {
    //   const height = wx.getSystemInfoSync().windowHeight
    //   return height - 225
    // }
  },
  onReady () {
    // const that = this
    // this.swiperHeight = wx.getSystemInfoSync().windowHeight - 265
    // wx.downloadFile({
    //   url: 'https://img.ccsc.work/test%2F3176f36b9cee7cbdef_10.mp4?OSSAccessKeyId=LTAIfNCN3kQQlBHl&Expires=1630107264&Signature=MpMLkHgtRZ5jzmye4ACSt%2BALejo%3D', // 仅为示例，并非真实的资源
    //   success (res) {
    //     that.playUrl[1].url = res.tempFilePath
    //     // 只要服务器有响应数据，就会把响应内容写入文件并进入 success 回调，业务需要自行判断是否下载到了想要的内容
    //     if (res.statusCode === 200) {
    //       wx.playVoice({
    //         filePath: res.tempFilePath
    //       })
    //     }
    //   }
    // })
  },
  methods: {
    init () {
      console.log('courseVideoList', this.$mp.query, this.courseVideoList)
      this.courseVideoList = JSON.parse(this.$mp.query.courseVideoList)
      const videoId = this.$mp.query.id
      this._getVideosInfo({}, videoId)
      this._getVideosUrl({}, videoId)
      this._getVideosComments({}, videoId)
    },
    swiperChange (e) {
      this.activeIndex = e.mp.detail.current
    },
    // 切换视屏
    goVideo () {
      // TODO: 视屏切换
    },
    // 跳去评论
    goComment (item, retirect) {
      // TODO: 后续改动mixins
      if (item) wx.setStorageSync('curCourseComent', item)
      wx.navigateTo({
        url: `../comment/main?id=${this.courseId}&type=video`
      })
    },
    // 视频详情
    _getVideosInfo (data, id) {
      this.$http.video.getVideosInfo(data, id).then(res => {
        this.videoInfo = res.data
      })
    },
    // 视频播放地址
    _getVideosUrl (data, id) {
      this.$http.video.getVideosUrl(data, id).then(res => {
        console.log('_getVideosUrl', res)
        this.playUrl = res
      })
    },
    // 视频评论提交
    _getVideosComments (data, id, merge) {
      this.$http.video.getVideosComments(data, id).then(res => {
        res.data.forEach(e => {
          e.created_format = formatTime(e.created_at)
        })
        if (merge) {
          this.videosComments = this.videosComments.concat(res.data)
        } else {
          this.videosComments = res.data
        }
      })
    }
  },
  created () {
    const logs = (wx.getStorageSync('logs') || [])
    this.logs = logs.map(log => formatTime(new Date(log)))
  },
  mounted () {
    this.videoId = this.$mp.query.id
    this.init()
  }
}
</script>

<style lang="less">
@import '../../assets/style/variable.less';

.video-info{
  position: relative;
  top: 0;
  left: 0;
  width: 100%;
  height: auto;
  .video-box{
    position: relative;
    top: 0;
    left: 0;
    width: 100%;
    height: 450rpx;
  }
  .tabBar{
    display: flex;
    position: relative;
    top: 0;
    left: 0;
    width: 100%;
    height: 40px;
    line-height: 40px;
    box-shadow: 0 2rpx 8rpx 0 rgba(0, 0, 0, 0.1);
    justify-content: center;
    &-item{
      display: flex;
      flex: 1;
      font-size: 14px;
      color: #777777;
      justify-content: center;
    }
    &-item-active{
      border-bottom: 3px solid red;
      color: red;
    }
  }
  .info-content{
    position: relative;
    // 详情
    .introduction{
      position: relative;
      width: 100%;
      height: auto;
      padding: 20px;
      box-sizing: border-box;
      &-title{
        display: flex;
        padding: 0 0 20px;
        border-bottom: 1px solid @border-color;
        &__name{
          flex: 1;
          font-size: 16px;
          color: #1A1A1A;
        }
        &__num{
          flex: 0 0 auto;
          font-size: 10px;
          color: @font-color-gray;
        }
      }
      &-lable{
        font-size: 16px;
        color: #1A1A1A;
        padding: 10px 0;
      }
      &-description, &-short_description{
        font-size: 13px;
        color: #777777;
        padding-left: 20px;
        box-sizing: border-box;
      }
    }
    // 列表
    .list-container{
      padding: 0 0 0 10px;
      position: relative;
      .list-item{
        display: flex;
        align-items: center;
        .video-icon{
          display: flex;
          flex: 0 0 auto;
          width: 32rpx;
          height: 32rpx;
          border: 1px solid #787878;
          align-items: center;
          justify-content: center;
          border-radius: 32rpx;
          margin-right: 10px;
          transform: rotate(-90deg);
          &__img{
            flex: 0 0 auto;
            width: 20rpx;
            height: auto;
            position: relative;
            top: 3rpx;
          }
        }
        .video-title{
          display: flex;
          flex: 1;
          font-size: 13px;
          line-height: 2.6;
          border-bottom: 1px solid @border-color;
        }
      }
    }
    // 评价
    .comments-list{
      padding: 20px;
      box-sizing: border-box;
      .list-item{
        display: flex;
        padding: 10px 0;
        border-bottom: 1px solid @border-color;
        .item-avatar{
          display: flex;
          flex: 0 0 auto;
          width: 30px;
          height: auto;
          border-radius: 20px;
          padding-right: 5px;
          box-sizing: border-box;
        }
        .item-content{
          display: flex;
          flex: 1;
          position: relative;
          flex-direction: column;
          &__name{
            position: relative;
            top: 0;
            left: 0;
            width: 100%;
            height: auto;
            font-size: 14px;
            color: darkslateblue;
          }
          &__time{
            position: relative;
            top: 0;
            right: 0;
            font-size: 10px;
            color: @font-color-gray;
          }
          &__content{
            position: relative;
            top: 0;
            left: 0;
            width: 100%;
            height: auto;
            padding: 5px 0;
            font-size: 12px;
            color: #1A1A1A;
          }
        }
      }
    }
  }
  .btn-submit{
    position: absolute;
    bottom: 20px;
    left: 50%;
    transform: translate(-50%, 0);
    font-size: 14px;
    color: #fff;
    width: 45px;
    height: 45px;
    line-height: 45px;
    text-align:center;
    border-radius: 22.5px;
    background: rgba(20, 181, 253, 1);
    box-shadow: 0 1px 2px rgba(20, 181, 253, .7);
    z-index: 1;
    opacity: .86;
  }
}

</style>
