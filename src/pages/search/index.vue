<template>
  <div>
    <div class="search">
      <icon type="search" size="20"></icon>
      <input type="text" @confirm="submit" v-model="search" class="search-content" placeholder="请输入想要搜索的商品">
      <button class="cancel" @click="clearVal" type="button">清空</button>
    </div>

    <!-- 搜索历史记录 -->
    <div class="history" v-show="goodsList.length == 0">
      <div class="title">
        <span>历史搜索</span>
        <span class="iconfont icon-shanchu" @click="clearHistory"></span>
      </div>
      <div class="history-content">
        <ul class="content">
          <li class="list" @click="searchVal" :data-val="item" v-for="(item, index) in history" :key="index">{{item}}</li>
        </ul>
      </div>
    </div>

    <!-- 搜索数据 -->
    <div class="content">
      <div class="title" v-show="goodsList.length !=0">
        <ul class="title-list">
          <li class="list-name" :class="{active:selectIndex == 0}" @click="changeSelected(0)">综合</li>
          <li class="list-name" :class="{active:selectIndex == 1}" @click="changeSelected(1)">销量</li>
          <li class="list-name" :class="{active:selectIndex == 2}" @click="changeSelected(2)">价格
            <div class="orderPrice">
              <span class="iconfont icon-jiantoushang" :class="{active:orderPrice==true}"></span>
              <span class="iconfont icon-jiantouxia" :class="{active:orderPrice==false}"></span>
            </div>
          </li>
        </ul>
      </div>
      <div class="list">
        <ul class="goods-list">
          <li class="goods-info" v-for="(item, index) in orderArr" :key="index">
            <a :href="'/pages/detail/main?id='+item.goods_id" class="goods">
              <img :src="item.goods_small_logo" alt="" class="goods-icon">
              <div class="goodsname">
                <span class="name">{{item.goods_name}}</span>
                <span class="price">￥{{item.goods_price}}</span>
              </div>
            </a>
          </li>
        </ul>
      </div>
    </div>

  </div>
</template>

<script>
// 导入工具函数
import tool from "../../utils/index";

export default {
  data: function() {
    return {
      history: [], // 搜索历史
      search: "", // 搜索内容
      goodsList: [], // 商品搜索列表
      goodsDeafult:[], // 商品默认数据
      selectIndex: 0, // 选择状态
      orderPrice: true //
    };
  },

  onLoad() {
    if (wx.getStorageSync("history")) {
      this.history = wx.getStorageSync("history");
    } else {
      this.history = [];
    }
  },

  methods: {
    // 根据输入内容搜索
    getData(){
       tool
        .thenAjax({
          url: `api/public/v1/goods/search?query=${this.search}`
        })
        .then(res => {
          // console.log(res);
          this.goodsList = res.data.message.goods;
          this.goodsDeafult = res.data.message.goods;
        });
    },
    // 搜索
    submit() {
      // indexOf会返回数组中该元素所在的位置
      let index = this.history.indexOf(this.search);

      // 不等于-1说明存在
      if (index != -1) {
        this.history.splice(index, 1);
      }
      // 将数据存入到storage中
      this.history.unshift(this.search);

      // 如果历史搜索记录的内容超过十条，就显示最新的十条
      if(this.history.length>10){
        this.history.pop()
      }
      wx.setStorage({
        key: "history",
        data: this.history
      });

      // 根据输入的值请求数据
     this.getData()
    },

    // 清空搜索框的内容
    clearVal() {
      this.search = "";
    },

    // 删除历史搜索
    clearHistory() {
      this.history = [];
      wx.setStorage({
        key: "history",
        data: this.history
      });
    },

    // 点击跳转到不同的栏目
    changeSelected(idx) {
      this.selectIndex = idx;
      if (idx == 2) {
        this.orderPrice = !this.orderPrice;
      }
    },

    // 点击搜索历史记录的列表去搜索
    searchVal(event){
      // console.log(event);
      this.search = event.target.dataset.val;

      // 根据值去搜索
      this.getData()
      
    }
  },

  // 计算属性
  computed:{
    // 按照不同的条件进行排序
    orderArr(){
      // 当为默认值时返回默认的数据
      if(this.selectIndex == 0){
        return this.goodsList;
        // 如果按销量排
      }else if (this.selectIndex == 1){
        this.goodsList.sort((a,b)=>{
          // 销量从大到小排
          return a.goods_id - b.goods_id
        });

        return this.goodsList;
      }else if (this.selectIndex == 2){
        this.goodsList.sort((a,b)=>{
          if(this.orderPrice == true){
            return a.goods_price - b.goods_price;
          }else {
            return b.goods_price - a.goods_price;
          }
        });
        return this.goodsList;
      }
    }
  }
};
</script>

<style lang="less">
.search {
  padding: 40rpx 16rpx;
  box-sizing: border-box;
  display: flex;
  background-color: #efefef;
  position: relative;
  align-items: center;
  .search-content {
    flex: 1;
    background-color: #fff;
    height: 80rpx;
    padding-left: 80rpx;
    border-radius: 10rpx;
    font-size: 28rpx;
    margin-right: 20rpx;
  }
  icon {
    position: absolute;
    z-index: 998;
    left: 30rpx;
    top: 60rpx;
  }
  .cancel {
    width: 180rpx;
    height: 80rpx;
    line-height: 80rpx;
    font-size: 28rpx;
  }
}

// 搜索历史
.history {
  padding: 0 16rpx;
  padding-top: 30rpx;
  box-sizing: border-box;
  .title {
    display: flex;
    justify-content: space-between;
    font-size: 40rpx;
    .iconfont {
      font-size: 44rpx;
      color: #ccc;
    }
  }
  // 搜索历史列表
  .history-content {
    .content {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      .list {
        height: 64rpx;
        line-height: 64rpx;
        border-radius: 10rpx;
        background-color: #ccc;
        padding: 0 20rpx;
        margin-right: 30rpx;
        margin-top: 20rpx;
        font-size: 28rpx;
      }
    }
  }
}

// 搜索数据
.content {
  .title {
    .title-list {
      display: flex;
      align-items: center;
      justify-content: space-around;
      border-bottom: 2rpx solid #ccc;
      height: 100rpx;
      .list-name {
        &.active {
          color: #ff2d4a;
        }
        &:last-child {
          display: flex;
          align-items: center;
          justify-content: center;
          .orderPrice {
            margin-top: 20rpx;
            margin-left: 10rpx;
            .iconfont {
              font-size: 24rpx;
              display: block;
              color: #000;
              &.active {
                color: #ccc;
              }
            }
          }
        }
      }
    }
  }

  // 搜索列表
  .list {
    .goods-list {
      box-sizing: border-box;
      .goods-info {
        height: 260rpx;
        padding: 30rpx 0;
        border-bottom: 2rpx solid #ccc;
        .goods {
          height: 100%;
          display: flex;
          align-items: center;
          padding: 0 20rpx;
          .goods-icon {
            display: block;
            width: 200rpx;
            height: 200rpx;
            margin: 0 auto;
          }
          .goodsname {
            flex: 1;
            display: flex;
            height: 200rpx;
            flex-direction: column;
            justify-content: space-between;
            margin-left: 20rpx;
            .name {
              font-size: 28rpx;
              display: block;
              overflow: hidden;
              text-overflow: ellipsis;
              display: -webkit-box;
              -webkit-box-orient: vertical;
              -webkit-line-clamp: 2;
            }
            .price {
              display: block;
              font-size: 36rpx;
              color: #ff2d4a;
            }
          }
        }
      }
    }
  }
}

</style>
