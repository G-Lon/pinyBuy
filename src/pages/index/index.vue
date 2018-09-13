<template>
  <div>
    <!-- 头部 -->
    <!-- <div class="header">
      <icon type="search" size="20"></icon>
      <input type="text" placeholder="搜索">
    </div> -->
    <myheader></myheader>

    <!-- 轮播图区域 -->
    <swiper indicator-dots circular autoplay duration indicator-color="" indicator-active-color="#fff">
      <swiper-item v-for="item in sliderList" :key="item.name">
        <image :src="item.image_src" />
      </swiper-item>
    </swiper>

    <!-- 导航 -->
    <div class="nav">
      <a href="#" class="navbar" v-for="(item, index) in navList" :key="index">
        <img class="icon" :src="item.image_src" alt="">
        <span class="title">{{item.name}}</span>
      </a>
    </div>

    <!-- 楼层 -->
    <div class="floor" v-for="(item, index) in floorList" :key="index">
      <div class="floor-header">
        <span class="title">{{item.floor_title.name}}</span>
        <img class="header-icon" :src="item.floor_title.image_src" alt="">
      </div>
      <div class="content">
        <a class="link" href="#" v-for="(it, idx) in item.product_list" :key="idx">
          <img class="content-icon" :src="it.image_src" alt="">
        </a>
      </div>
    </div>

    <!-- 返回顶部 -->
    <div class="top" v-show="isShow == true" @click="toTop">
      <span class="iconfont icon-jiantoushang"></span>
      <span>返回顶部</span>
    </div>

    <!-- 底部 -->
    <div class="footer">
      <span class="iconfont icon-xiao"></span>
      <span>我是有底线的..</span>
    </div>

  </div>
</template>

<script>
// 引入thenAjax
import tool from "../../utils/index";

// 导入头部组件
import myheader from "../../components/header";

export default {
  data() {
    return {
      sliderList: [], // 轮播图列表
      navList: [], // 分类导航列表
      floorList: [], // 楼层数据
      isShow: false // 返回顶部显示状态
    };
  },
  // 注册头部组件
  components: {
    myheader
  },

  // 监听用户滑动页面事件。
  onPageScroll(scrollTop) {
    // console.log(scrollTop);
    if(scrollTop.scrollTop > 192){
      this.isShow = true;
    }else {
      this.isShow = false;
    }
    
  },

  methods: {
    // 返回顶部
    toTop() {
      wx.pageScrollTo({
        scrollTop: 0,
        duration: 500
      });
    }
  },
  onLoad() {
    tool
      .thenAjax({
        url: "api/public/v1/home/swiperdata"
      })
      .then(res => {
        // console.log(res);
        this.sliderList = res.data.message;
        return tool.thenAjax({
          url: "api/public/v1/home/catitems"
        });
      })
      .then(res => {
        // console.log(res);
        this.navList = res.data.message;
        // 楼层
        return tool.thenAjax({
          url: "api/public/v1/home/floordata"
        });
      })
      .then(res => {
        // console.log(res);
        this.floorList = res.data.message;
      });
  }
};
</script>

<style lang="less">
page {
  padding-top: 100rpx;
}

// // 头部
// .header {
//   padding: 20rpx 16rpx;
//   position: fixed;
//   left: 0;
//   top: 0;
//   width: 100%;
//   z-index: 998;
//   box-sizing: border-box;
//   background-color: #ff2d4a;
//   icon {
//     position: absolute;
//     top: 30rpx;
//     left: 50%;
//     transform: translateX(-200%);
//     z-index: 988;
//   }
//   input {
//     width: 100%;
//     height: 60rpx;
//     background-color: #fff;
//     text-align: center;
//     font-size: 24rpx;
//     border-radius: 10rpx;
//   }
// }

// 轮播图
swiper {
  height: 384rpx;
  swiper-item {
    image {
      display: block;
      width: 100%;
      height: 100%;
    }
  }
}

// 导航
.nav {
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 24rpx 0;
  .navbar {
    text-align: center;
    .icon {
      display: block;
      margin: 0 auto;
      width: 100rpx;
      height: 100rpx;
    }
    .title {
      font-size: 28rpx;
    }
  }
}

// 楼层
.floor {
  // 楼层头
  .floor-header {
    position: relative;
    height: 100rpx;
    padding: 20rpx 0;
    .header-icon {
      width: 100%;
      height: 100%;
    }
    .title {
      position: absolute;
      left: 20rpx;
      top: 40rpx;
      font-size: 48rpx;
      color: #fe7b94;
    }
  }
  // 咯曾内容
  .content {
    height: 440rpx;
    .link {
      float: left;
      width: 33.333%;
      height: 220rpx;
      padding: 5rpx;
      box-sizing: border-box;

      &:nth-child(1) {
        height: 100%;
      }
      .content-icon {
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 8rpx;
      }
    }
  }
}

// 底部
.footer {
  padding-top: 20rpx;
  padding-bottom: 20rpx;
  background-color: #f4f4f4;
  text-align: center;
  color: #aaaaaa;
  font-size: 32rpx;
  .iconfont {
    font-size: 48rpx;
  }
}

// 返回顶部
.top {
  position: fixed;
  z-index: 998;
  width: 90rpx;
  height: 90rpx;
  background-color: #eee;
  border-radius: 50%;
  bottom: 90rpx;
  right: 20rpx;
  font-size: 20rpx;
  text-align: center;
  .iconfont {
    display: block;
  }
}
</style>
