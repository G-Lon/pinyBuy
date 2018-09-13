<template>
    <div>
        <!-- 头部 -->
        <div class="header">
            <icon type="search" size="20"></icon>
            <input @click="search" type="text" placeholder="搜索">
        </div>
    </div>
</template>

<script>
export default {
  data: function() {
    return {};
  },
  methods: {
    search() {
        wx.navigateTo({
            url: '/pages/search/main'
        });
    }
  }
};
</script>

<style lang="less">
// 头部
.header {
  padding: 20rpx 16rpx;
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  z-index: 998;
  box-sizing: border-box;
  background-color: #ff2d4a;
  icon {
    position: absolute;
    top: 30rpx;
    left: 50%;
    transform: translateX(-200%);
    z-index: 988;
  }
  input {
    width: 100%;
    height: 60rpx;
    background-color: #fff;
    text-align: center;
    font-size: 24rpx;
    border-radius: 10rpx;
  }
}
</style>


