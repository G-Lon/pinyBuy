<template>
  <div>
    <!-- 商品介绍轮播图 -->
    <swiper indicator-dots autoplay circular>
      <block v-for="(item, index) in goodsInfo.pics" :key="index">
        <swiper-item>
          <image :src="item.pics_mid_url" class="slide-image" />
        </swiper-item>
      </block>
    </swiper>

    <!-- 商品价格信息 -->
    <div class="goodsInfo">
      <div class="left">
        <span class="price">￥{{goodsInfo.goods_price}}</span>
        <span class="goodsname">{{goodsInfo.goods_name}}</span>
        <span class="express">快递：&nbsp;&nbsp;免运费</span>
      </div>
      <div class="right">
        <span class="iconfont icon-shoucang"></span>
        <span class="collect">收藏</span>
      </div>
    </div>

    <!-- 其他信息 -->
    <div class="otherInfo">
      <div class="discount">
        促销
        <span class="item">满300减30元</span>

      </div>
      <div class="choosed">
        已选
        <span class="item">绿色/XS/1件</span>

      </div>
    </div>

    <!-- 地址 -->
    <div class="address" @click="chooseAddress">
      送至
      <sapn class="item">
        <span class="add gray">{{address}}
          <span class="iconfont icon-jiantouyou"></span>
        </span>
      </sapn>
    </div>

    <!-- 商品参数，图文介绍 -->
    <div class="desc">
      <div class="item">
        <span class="item-list" @click="changeSelect(0)" :class="{active:selectIndex == 0}">图文介绍</span>
        <span class="item-list" @click="changeSelect(1)" :class="{active:selectIndex == 1}">规格参数</span>
      </div>
      <div class="introduce-content" v-show="selectIndex ==0">
        <wxParse :content="goodsInfo.goods_introduce" @preview="preview" @navigate="navigate" />
      </div>
      <div class="params-content" v-show="selectIndex ==1">
        <div class="attr" v-for="(item, index) in goodsInfo.attrs" :key="index">
          <span class="attr-name">{{item.attr_name}}</span>
          <span class="attr-val">{{item.attr_vals}}</span>
        </div>
      </div>
    </div>

    <!-- 底部控件 -->
    <div class="footer">
      <div class="kefu">
        <span class="iconfont icon-kefu"></span>
        联系客服
      </div>
      <div class="cart" @click="toCart">
        <span class="iconfont icon-gouwuche"></span>
        购物车
      </div>
      <div>
        <button class="addCart" @click="addCart">加入购物车</button>
        <button class="buy">立即购买</button>
      </div>

    </div>
  </div>
</template>

<script>
// 导入数据请求方法
import tool from "../../utils/index";

// 引入第三方的wxParse
import wxParse from "mpvue-wxparse";

export default {
  // 给wxParse注册组件
  components: {
    wxParse
  },
  data() {
    return {
      goods_id: 0, // 商品Id
      goodsInfo: [], // 商品详情图片
      selectIndex: 0, //
      address: "" // 地址
    };
  },

  onLoad(options) {
    // options的数据是上一个页面的query
    // console.log(options);
    this.goods_id = options.id;
    tool
      .thenAjax({
        url: `api/public/v1/goods/detail?goods_id=${options.id}`
      })
      .then(res => {
        // console.log(res);
        this.goodsInfo = res.data.message;
      });
  },

  methods: {
    // tab栏的切换

    changeSelect(idx) {
      this.selectIndex = idx;
    },

    // 选择地址
    chooseAddress() {
      wx.chooseAddress({
        success: res => {
          // console.log(res.userName);
          // console.log(res.postalCode);
          // console.log(res.provinceName);
          // console.log(res.cityName);
          // console.log(res.countyName);
          // console.log(res.detailInfo);
          // console.log(res.nationalCode);
          // console.log(res.telNumber);
          this.address =
            res.provinceName + " " + res.cityName + " " + res.countyName;
        }
      });
    },

    // 点击加入购物车
    addCart() {
      // 读取缓存的数据
      let cart = wx.getStorageSync("cart");
      if (cart) {
        // 如果有
        if (cart[this.goods_id]) {
          // 如果该商品已经加入过,就在原来的基础上加上1
          cart[this.goods_id] += 1;
        } else {
          // 如果没有添加,购买数量就为1
          cart[this.goods_id] = 1;
        }
      } else {
        // 如果没有
        // cart = {
        //   // 默认属性名 无论些什么 都会直接解析为 属性名{ goods_id:}
        //   // 加上中括号的目的是 把这个变量(表达式解析了)把值放在那个位置
        //   [this.goods_id]:1
        // }
        cart = {};
        cart[this.goods_id] = 1;
      }

      wx.setStorageSync("cart", cart);
    },

    // 去购物车
    toCart() {
      // 购物车页面是由tabbar管理维护的
      // 这种页面的跳转方式不是 navigateTo
      // switchTab即可
      // wx.navigateTo({
      //   url: '/pages/cart/main'
      //  });

      wx.switchTab({
        url: "/pages/cart/main"
      });
    },

    // 插件方法
    preview(src, e) {
      // do something
    },
    navigate(href, e) {
      // do something
    }
  },

  created() {}
};
</script>

<style lang="less">
// wxParse的css
@import url("~mpvue-wxparse/src/wxParse.css");
page {
  box-sizing: border-box;
  padding-bottom: 100rpx;
  background-color: #f4f4f4;
}
// 轮播图
swiper {
  height: 720rpx;
  width: 100%;
  swiper-item {
    image {
      display: block;
      width: 100%;
      height: 100%;
    }
  }
}

// 商品信息
.goodsInfo {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #fff;
  .left {
    padding-left: 20rpx;
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    .price {
      color: #ff2d4a;
    }
    .goodsname {
      font-size: 28rpx;
      margin: 30rpx 0;
    }
    .express {
      font-size: 22rpx;
      color: #bbb;
    }
  }
  .right {
    width: 180rpx;
    text-align: center;
    color: #aaa;
    .iconfont {
      display: block;
    }
    .collect {
      font-size: 24rpx;
    }
  }
}

// 其他信息
.otherInfo {
  margin: 30rpx 0;
  background-color: #fff;
  padding-left: 20rpx;
  .discount {
    height: 100rpx;
    line-height: 100rpx;
    font-size: 28rpx;
    .item {
      margin-left: 30rpx;
      color: #ff2d4a;
    }
  }
  .choosed {
    height: 100rpx;
    line-height: 100rpx;
    font-size: 28rpx;
    .item {
      margin-left: 30rpx;
      color: #aaa;
    }
  }
}

// 地址
.address {
  height: 100%;
  background-color: #fff;
  line-height: 100rpx;
  padding-left: 20rpx;
  font-size: 28rpx;
  .item {
    color: #bbb;
    margin-left: 30rpx;
    .add {
      .iconfont {
        font-size: 24rpx;
      }
    }
  }
}

// 参数
.desc {
  background-color: #fff;
  .item {
    display: flex;
    justify-content: space-around;
    height: 100rpx;
    align-items: center;
    font-size: 30rpx;
    .item-list {
      height: 100%;
      width: 50%;
      text-align: center;
      line-height: 100rpx;
      &.active {
        color: #ff2d4a;
        border-bottom: 3px solid #ff2d4a;
      }
    }
  }
}

// 参数对应的内容
.introduce-content {
  .wxParse .p {
    margin: 0;
    image {
      display: block;
    }
  }
}

.params-content {
  background-color: #fff;
  padding: 8rpx;
  .attr {
    height: 100rpx;
    line-height: 100rpx;
    display: flex;
    font-size: 28rpx;
    &:not(:first-child) {
      margin-top: -1rpx;
    }
    .attr-name {
      display: block;
      flex: 1;
      text-align: center;
      border: 1rpx solid #000;
    }
    .attr-val {
      display: block;
      flex: 1;
      padding-left: 40rpx;
      border: 1rpx solid #000;
      border-left: 0;
    }
  }
}

// 底部控件
.footer {
  box-sizing: border-box;
  height: 100rpx;
  width: 100%;
  background-color: #fff;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-left: 20rpx;
  position: fixed;
  left: 0;
  bottom: 0;
  overflow: hidden;
  .kefu {
    font-size: 24rpx;
    color: #666;
    text-align: center;
    .iconfont {
      display: block;
    }
  }
  .cart {
    font-size: 24rpx;
    color: #666;
    text-align: center;
    .iconfont {
      display: block;
    }
  }
  .addCart {
    width: 210rpx;
    height: 100rpx;
    color: white;
    text-align: center;
    line-height: 100rpx;
    font-size: 28rpx;
    border-radius: 0;
    background-color: #ffb517;
    margin: 0;
    display: inline-block;
    border: 0;
  }
  .buy {
    width: 210rpx;
    height: 100rpx;
    color: white;
    text-align: center;
    line-height: 100rpx;
    font-size: 28rpx;
    border-radius: 0;
    background-color: #ff2d4a;
    margin: 0;
    display: inline-block;
    border: 0;
  }
}
</style>
