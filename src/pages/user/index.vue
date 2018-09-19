<template>
  <div>
    <div class="user-header">
      <!-- 获取用户信息时需要使用button 并且需要加上open-type来增强开放能力 还要绑定bindgetuserinfo -->
      <button open-type="getUserInfo" @getuserinfo="userInfo">
        <img :src="icon" alt="" class="icon">
      </button>

      <span class="iconfont icon-shezhi"></span>
      <span class="iconfont icon-xiaoxi"></span>
      <span class="info">{{info}}</span>
    </div>
    <!-- 收藏信息 -->
    <div class="info-box">
      <div class="item-list">
        <div class="item">
          <p class="num">0</p>
          <p class="title">收藏的店铺</p>
        </div>
        <div class="item">
          <p class="num">0</p>
          <p class="title">收藏的商品</p>
        </div>
        <div class="item">
          <p class="num">0</p>
          <p class="title">关注的商品</p>
        </div>
        <div class="item">
          <p class="num">0</p>
          <p class="title">我的足迹</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      icon: "/static/images/icon.png",
      info: "登录/注册"
    };
  },

  methods: {
    userInfo(res) {
      // console.log(res);
      this.icon = res.mp.detail.userInfo.avatarUrl;
      this.info = res.mp.detail.userInfo.nickName;
    }
  }
};
</script>

<style lang="less">
.user-header {
  height: 420rpx;
  background-color: #ff2d4a;
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: center;
  button {
    width: 140rpx;
    height: 140rpx;
    border: 3px solid #fff;
    border-radius: 50%;
    padding: 0;
    .icon {
      display: block;
      width: 100%;
      height: 100%;
    }
  }

  .icon-shezhi {
    position: absolute;
    left: 50%;
    margin-left: -200rpx;
    top: 175rpx;
    color: #fff;
  }
  .icon-xiaoxi {
    position: absolute;
    right: 50%;
    margin-right: -200rpx;
    top: 175rpx;
    color: #fff;
  }
  .info {
    display: block;
    margin: 0 auto;
    margin-top: 20rpx;
    color: #fff;
  }
}

.info-box {
  .item-list {
    display: flex;
    width: 720rpx;
    height: 120rpx;
    justify-content: space-around;
    align-items: center;
    margin: 0 auto;
    transform: translateY(-30rpx);
    z-index: 998;
    background-color: #fff;
    .item {
      flex: 1;
      font-size: 28rpx;
      text-align: center;
      .num {
        color: #aaa;
      }
    }
  }
}
</style>
