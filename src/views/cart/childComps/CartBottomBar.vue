<template>
  <div class="bottom-bar">
    <div class="check-content">
      <check-button :is-checked="isSelectAll" class="check-button" @click.native="checkClick"></check-button>
      <span>全选</span>
    </div>
    <div class="price">合计： {{totalPrice}}</div>
    <div class="calculate" @click="calcClick">去计算： ({{checkLength}})</div>
  </div>
</template>

<script>
import CheckButton from "components/content/checkButton/CheckButton";

import { mapGetters } from "vuex";

export default {
  name: "CartBottomBar",
  components: {
    CheckButton
  },
  computed: {
    ...mapGetters(["cartList"]),
    totalPrice() {
      return (
        "￥" +
        this.cartList
          .filter(item => {
            return item.checked;
          })
          .reduce((preValue, item) => {
            return preValue + item.price * item.count;
          }, 0)
          .toFixed(2)
      );
    },
    checkLength() {
      return this.cartList.filter(item => item.checked).length;
    },
    isSelectAll() {
      //   如果购物车没有商品，那么全选按钮则不被选中
      if (this.cartList.length === 0) return false;
      //如果找到有一个没有checked属性的 就返回false
      //1.使用filter
      //   return !(this.cartList.filter(item => !item.checked).length);
      //2.使用find
      return !this.cartList.find(item => !item.checked);
    }
  },
  methods: {
    checkClick() {
      if (this.isSelectAll) {
        //全部选中时
        this.cartList.forEach(item => (item.checked = false));
      } else {
        //部分或全部不选中
        this.cartList.forEach(item => (item.checked = true));
      }
      // this.cartList.forEach(item => item.checked = !this.isSelectAll)
    },
    calcClick() {
      if (!this.checkLength) {
        this.$toast.show("请选择购买的商品");
      }
    }
  }
};
</script>

<style scoped>
.bottom-bar {
  display: flex;
  position: relative;
  bottom: 49px;

  height: 40px;
  background-color: #eee;
  line-height: 40px;
  font-size: 14px;
}

.check-content {
  display: flex;
  align-items: center;
  margin-left: 10px;
  width: 60px;
}

.check-button {
  width: 20px;
  height: 20px;
  line-height: 20px;
  margin-right: 5px;
}
.price {
  margin-left: 20px;
  flex: 1;
}
.calculate {
  width: 90px;
  background-color: red;
  color: #fff;
  text-align: center;
}
</style>>
