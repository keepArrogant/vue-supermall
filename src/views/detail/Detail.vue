<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"></detail-nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <detail-swiper :top-images="topImages"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="detailGoodsInfoImageLoad"></detail-goods-info>
      <detail-param-info ref="params" :param-info="paramInfo"></detail-param-info>
      <detail-comment-info ref="comment" :comment-info="commentInfo"></detail-comment-info>
      <goods-list ref="recommend" :goods="recommends"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>
    <detail-bottom-bar @addToCart="addToCart"></detail-bottom-bar>
    <toast :message="message" :show="show"></toast>
  </div>
</template>

<script>
import DetailNavBar from "./childComps/DetailNavBar";
import DetailSwiper from "./childComps/DetailSwiper";
import DetailBaseInfo from "./childComps/DetailBaseInfo";
import DetailShopInfo from "./childComps/DetailShopInfo";
import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
import DetailParamInfo from "./childComps/DetailParamInfo";
import DetailCommentInfo from "./childComps/DetailCommentInfo";
import DetailBottomBar from "./childComps/DetailBottomBar";

import Scroll from "components/common/scroll/Scroll";
import GoodsList from "components/content/goods/GoodsList";
import Toast from "components/common/toast/Toast";

import { debounce } from "common/utils";
import { itemListenerMixin, backTopMixin } from "common/mixin";

//通过映射方式调用vuex中的actions属性中的方法
import { mapActions } from "vuex";

import {
  getDetail,
  Goods,
  Shop,
  GoodsParam,
  getRecommend
} from "network/detail";

export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      themeTopYs: [],
      getThemeTopY: {},
      currentIndex: 0,
      message: "",
      show: false
    };
  },
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    Scroll,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    DetailBottomBar,
    GoodsList,
    Toast
  },
  mixins: [itemListenerMixin, backTopMixin],
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      console.log(res);
      // 1.获取数据
      const data = res.result;
      // 2.获取顶部的图片轮播数据

      this.topImages = data.itemInfo.topImages;

      // 3.获取商品信息
      this.goods = new Goods(
        data.itemInfo,
        data.columns,
        data.shopInfo.services
      );

      // 4.创建店铺信息的对象
      this.shop = new Shop(data.shopInfo);

      // 5.保存商品的详细数据
      this.detailInfo = data.detailInfo;

      // 6.获取参数的信息
      this.paramInfo = new GoodsParam(
        data.itemParams.info,
        data.itemParams.rule
      );

      // 7.取出评论的信息
      if (data.rate.cRate !== 0) {
        this.commentInfo = data.rate.list[0];
      }

      // 8.给getThemeTopY赋值(对给this.themeTopYs赋值的操作进行防抖)
      this.getThemeTopY = debounce(() => {
        // console.log("----");
        this.themeTopYs = [];
        this.themeTopYs.push(0);
        this.themeTopYs.push(this.$refs.params.$el.offsetTop);
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
        this.themeTopYs.push(Number.MAX_VALUE);
        console.log(this.themeTopYs);
      }, 100);
    });

    // 3.请求推荐数据
    getRecommend().then(res => {
      this.recommends = res.data.list;
    });

    //第一次获取，值不对，值不对的原因：this.$refs.params.$el没有渲染
    // this.themeTopYs = [];
    // this.themeTopYs.push(0);
    // this.themeTopYs.push(this.$refs.params.$el.offsetTop);
    // this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
    // this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
    // console.log(this.themeTopYs);

    // this.$nextTick(() => {
    //   // 第二次获取，值也不对
    //   // 值不对的原因是：图片 根据最新的数据，对应的DOM是已经被渲染出来 但是图片依然是没有加载完(目前获取到的offsetTop不包含其中的图片)
    //   // offsetTop值不对的时候，都是因为图片没加载完的原因
    //   this.themeTopYs = [];
    //   this.themeTopYs.push(0);
    //   this.themeTopYs.push(this.$refs.params.$el.offsetTop);
    //   this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
    //   this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
    //   console.log(this.themeTopYs);
    // });
  },
  mounted() {
    console.log("mounted");
  },
  destroyed() {
    this.$bus.$off("itemImgLoad", this.itemImgListener);
  },
  methods: {
    ...mapActions(["addCart"]),
    detailGoodsInfoImageLoad() {
      // console.log("---");
      // this.newRefresh();
      this.$refs.scroll.refresh();
      this.$nextTick(() => {
        this.getThemeTopY();
      });
    },
    titleClick(index) {
      this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 200);
      console.log(index);
    },
    backClick() {
      this.backTop();
    },
    contentScroll(position) {
      // 1.获取y值
      const positionY = -position.y;
      // 2.positionY和主题中的值进行对比
      // [0, 735, 2157, 2352]
      // positionY 在 0 ~ 735 ， index = 0
      // positionY 在 =735 ~ 2157 ， index = 1
      // positionY 在 =2157 ~ 2352 ， index = 2
      // positionY 大于等于2352 ， index = 3
      // 现在我想在最后一个值也是和前面一样 有区间的比对，那么我们在它后面加上一个高度的最大值
      // 就能达到 positionY =2352 ~ Number.MAX_VALUE

      let length = this.themeTopYs.length;
      for (let i = 0; i < length - 1; i++) {
        if (
          // 第一种方法，普通而且性能不好
          // this.currentIndex !== i &&
          // ((i < length - 1 &&
          //   positionY >= this.themeTopYs[i] &&
          //   positionY < this.themeTopYs[i + 1]) ||
          //   (i === length - 1 && positionY >= this.themeTopYs[i]))
          // 第二种方法
          this.currentIndex !== i &&
          positionY >= this.themeTopYs[i] &&
          positionY < this.themeTopYs[i + 1]
        ) {
          this.currentIndex = i;
          this.$refs.nav.currentIndex = this.currentIndex;
        }
      }
      this.listenShowBackTop(position);
    },
    addToCart() {
      // 1.获取购物车需要展示的信息
      const product = {};
      product.image = this.topImages[0];
      product.title = this.goods.title;
      product.desc = this.goods.desc;
      product.price = this.goods.realPrice;
      product.iid = this.iid;
      console.log(product);

      // 2.将商品添加到购物车里
      // this.$store.commit("addCart", product);
      //普通的调用vuex中的actions中的方法
      // this.$store.dispatch("addCart", product).then(res => {
      //   console.log(res)
      // })
      // 通过映射的方式调用vuex中的actions中的方法
      // this.addCart(product).then(res => {
      //   this.show = true;
      //   this.message = res;

      //   setTimeout(() => {
      //     this.show = false;
      //     this.message = "";
      //   }, 1500);
      //   console.log(res);
      // });

      // $toast在vue实例被创建时跟随着创建，并且toast组件存放在一个div里，一般时刻是不会显示的
      this.addCart(product).then(res => {
        this.$toast.show(res);
      });
    }
  }
};
</script>

<style scoped>
#detail {
  position: relative;
  z-index: 9;
  background-color: #fff;
  height: 100vh;
}
.detail-nav {
  position: relative;
  z-index: 9;
  background-color: #fff;
}
.content {
  position: relative;
  height: calc(100% - 99px);
  overflow: hidden;
}
</style>
