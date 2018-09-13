<template>
  <div>
    <!-- 头部 -->
    <myheader></myheader>

    <!-- 分类 -->
    <div class="category">
      <div class="left">
        <scroll-view scroll-y scroll-with-animation>
          <div class="content" :class="{active:index == selectIndex}" v-for="(item, index) in cateList" :key="index" @click="selected(index)">{{item.cat_name}}</div>
        </scroll-view>
      </div>
      <div class="right">
        <scroll-view scroll-y scroll-with-animation>
          <!-- 广告图片 -->
          <img class="ad-icon" src="/static/images/titleImage.png" alt="">

          <div class="section" v-for="(item, index) in cateList[selectIndex].children" :key="index">
            <span class="title">/ {{item.cat_name}} /</span>
            <div class="list">
              <a href="#" class="section-list" v-for="(it, idx) in item.children" :key="idx">
                <img class="section-icon" :src="'https://autumnfish.cn/wx/'+it.cat_icon" alt="">
                <span class="section-name">{{it.cat_name}}</span>
              </a>
            </div>
          </div>
        </scroll-view>
      </div>
    </div>
  </div>
</template>

<script>
// 导入thenAjax
import tool from "../../utils/index";

// 导入头部组件
import myheader from "../../components/header";

export default {
  data: function() {
    return {
      cateList: [], // 分类列表
      selectIndex: 0 // 选中分类的下标
    };
  },
  // 注册组件
  components: {
    myheader
  },
  // 事件
  methods: {
    // 点击选中分类
    selected(index) {
      // console.log(index);
      this.selectIndex = index;
    }
  },
  // 小程序周期函数
  // 只执行一次
  onLoad() {
    tool
      .thenAjax({
        url: "api/public/v1/categories"
      })
      .then(res => {
        console.log(res);
        this.cateList = res.data.message;
      });
  }
};
</script>

<style lang="less">
page {
  padding-top: 100rpx;
  height: 100%;
  > view {
    height: 100%;
  }
}

.category {
  display: flex;
  height: 100%;
  padding-top: 20rpx;
  .left {
    width: 200rpx;
    height: 100%;
    scroll-view {
      height: 100%;
      background-color: #ccc;
      .content {
        height: 100rpx;
        border-bottom: 2rpx solid #eee;
        text-align: center;
        line-height: 100rpx;
        font-size: 28rpx;
        position: relative;
        &::before {
          // 一定需要设定content
          content: "";
          width: 8rpx;
          height: 60rpx;
          background-color: #eb4450;
          position: absolute;
          left: 0;
          top: 20rpx;
        }
        &.active {
          background-color: #fff;
          color: #eb4450;
        }
      }
    }
  }
  .right {
    height: 100%;
    flex: 1;
    padding: 0 16rpx;
    box-sizing: border-box;
    scroll-view {
      height: 100%;
      text-align: center;
      .ad-icon {
        display: block;
        width: 100%;
        height: 180rpx;
      }
      .section {
        .title {
          display: block;
          padding: 30rpx 0;
        }
        .list {
          display: flex;
          flex-wrap: wrap;
          .section-list {
            display: block;
            width: 33.333%;
            text-align: center;
            .section-icon {
              display: block;
              width: 95rpx;
              height: 90rpx;
              margin: 0 auto;
            }
            .section-name {
              font-size: 28rpx;
            }
          }
        }
      }
    }
  }
}
</style>


