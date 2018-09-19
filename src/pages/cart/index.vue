<template>
  <div>
    <!-- 收件人信息 -->
    <div class="cart-header">
      <div class="receive" @click="chooseAdd">
        <div class="left">
          收货人：{{userName}}
        </div>
        <div class="right">{{telNumber}}
          <span class="iconfont icon-jiantouyou"></span>
        </div>
      </div>
      <div class="address">
        收货地址：{{address}}
      </div>
    </div>

    <!-- 购物车列表 -->
    <div class="cart-content">
      <div class="title">
        <span class="iconfont icon-dianpu"></span>
        优购生活馆
      </div>
      <div class="cart-list" v-for="(item, index) in cartList" :key="index">
          <div class="left">
            <input type="radio" @click="changeChecked(index)" :checked="item.selected" color="#ff2d4a">
          </div>
          <div class="right">
            <img :src="item.goods_small_logo" class="img" alt="">
            <div class="goodsInfo">
              <div class="name">{{item.goods_name}}</div>
              <div class="control">
                <div class="price">￥{{item.goods_price}}.00</div>
                <div class="num-control">
                  <span class="sub" @click="sub(index)">-</span>
                  <span class="num">{{item.buyCount}}</span>
                  <span class="add" @click="add(index)">+</span>
                </div>
              </div>
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
// 导入封装的函数
import tool from '../../utils/index';

export default {
  data() {
    return {
      userName: "", //收件人
      telNumber: "", // 收件人电话
      address: "", // 收件人地址
      cartList:[], // 购物车列表
      checked:true, // 选中状态 
    };
  },

  methods: {
    // 获取地址信息
    chooseAdd() {
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

          this.userName = res.userName;
          this.telNumber = res.telNumber;
          this.address =
            res.provinceName + " " + res.cityName + " " + res.countyName;
        }
      });
    },

    // 选中的状态
    changeChecked(index){
      this.cartList[index].selected = !this.cartList[index].selected;
    },

    // 改变，减少
    sub(index){

    }
  },
  onLoad(){
    let cartIds = wx.getStorageSync('cart');
    let ids = '';
    for(const key in cartIds){
      ids += key;
      ids+= ',';
    }
    ids = ids.slice(0,-1);

    tool.thenAjax({
      url:`api/public/v1/goods/goodslist?goods_ids=${ids}`
    }).then(res=>{
      // console.log(res);
      res.data.message.forEach(v => {
          v.selected = true;
          v.buyCount = cartIds[v.goods_id];
      });
      this.cartList = res.data.message;
    })
  }
};
</script>

<style lang="less">
// 头部
.cart-header {
  padding: 10rpx 20rpx;
  font-size: 30rpx;
  border-bottom: 10rpx solid #ccc;
  .receive {
    display: flex;
    justify-content: space-between;
    height: 80rpx;
    align-items: center;
    .right {
      .iconfont {
        color: #bbb;
        font-size: 24rpx;
        margin-left: 30rpx;
      }
    }
  }
  .address {
    height: 80rpx;
    line-height: 80rpx;
  }
}

// 详细信息
.cart-content {
  .title {
    height: 100rpx;
    font-size: 30rpx;
    line-height: 100rpx;
    border-bottom: 1rpx solid #ccc;
    .iconfont {
      padding-left: 30rpx;
      font-size: 34rpx;
      color: #bbb;
    }
  }
  .cart-list {
    height: 220rpx;
    display: flex;
    align-items: center;
    .left{
      width: 100rpx;
      input {
        display: block;
        width: 100%;
        text-align: center;
      }
    }
    .right{
      flex: 1;
      display: flex;
      border-bottom: 1rpx solid #ccc;
      align-items: center;
      padding: 10rpx;
      .img {
        display: block;
        width: 160rpx;
        height: 160rpx;
        margin-right: 20rpx;
      }
      .goodsInfo{
        flex: 1;
        height: 200rpx;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        .name{
          overflow: hidden;
          text-overflow: ellipsis;
          display: -webkit-box;
          -webkit-box-orient: vertical;
          -webkit-line-clamp: 2;
          font-size: 32rpx;
        }
        .control{
          display: flex;
          justify-content: space-between;
          .price{
            font-size: 36rpx;
            font-weight: 700;
            color: #ff2d4a;
          }
          .num-control{
            .sub{
              width: 60rpx;
              height: 50rpx;
              display: inline-block;
              border: 1px solid #000;
              text-align: center;
              line-height: 50rpx;
            }
            .num{
              display: inline-block;
              width: 60rpx;
              height: 50rpx;
              line-height: 50rpx;
              margin: 0 5rpx;
              text-align: center;
              font-size: 28rpx;
            }
            .add{
              width: 60rpx;
              height: 50rpx;
              display: inline-block;
              border: 1px solid #000;
              text-align: center;
              line-height: 50rpx;
            }
          }
        }
      }
    }
  }
}
</style>
