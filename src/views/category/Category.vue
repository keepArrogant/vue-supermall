<template>
  <div class="wrapper" ref="aaaa">
    <ul class="content">
      <button @click="btnClick">点击</button>
      <li>分类商品1</li>
      <li>分类商品2</li>
      <li>分类商品3</li>
      <li>分类商品4</li>
      <li>分类商品5</li>
      <li>分类商品6</li>
      <li>分类商品7</li>
      <li>分类商品8</li>
      <li>分类商品9</li>
      <li>分类商品10</li>
      <li>分类商品11</li>
      <li>分类商品12</li>
      <li>分类商品13</li>
      <li>分类商品14</li>
      <li>分类商品15</li>
      <li>分类商品16</li>
      <li>分类商品17</li>
      <li>分类商品18</li>
      <li>分类商品19</li>
      <li>分类商品20</li>
      <li>分类商品21</li>
      <li>分类商品22</li>
      <li>分类商品23</li>
      <li>分类商品24</li>
      <li>分类商品25</li>
      <li>分类商品26</li>
      <li>分类商品27</li>
      <li>分类商品28</li>
      <li>分类商品29</li>
      <li>分类商品30</li>
      <li>分类商品31</li>
      <li>分类商品32</li>
      <li>分类商品33</li>
      <li>分类商品34</li>
      <li>分类商品35</li>
      <li>分类商品36</li>
      <li>分类商品37</li>
      <li>分类商品38</li>
      <li>分类商品39</li>
      <li>分类商品40</li>
      <li>分类商品41</li>
      <li>分类商品42</li>
      <li>分类商品43</li>
      <li>分类商品44</li>
      <li>分类商品45</li>
      <li>分类商品46</li>
      <li>分类商品47</li>
      <li>分类商品48</li>
      <li>分类商品49</li>
      <li>分类商品50</li>
    </ul>
  </div>
</template>

<script>
import BScroll from "better-scroll";

export default {
  name: "Category",
  data() {
    return {
      scroll: null
    };
  },
  //组件创建完后调用  在created阶段，对浏览器来说，渲染整个HTML文档时,dom节点、css规则树与js文件被解析后，但是没有进入被浏览器render过程，上述资源是尚未挂载在页面上，也就是在vue生命周期中对应的阶段，实例已经被初始化，但是还没有挂载至$el上，所以我们无法获取到对应的节点，但是此时我们是可以获取到vue中data与methods中的数据的
  created() {
    // console.log(document.querySelector(".wrapper"));
    // this.scroll = new BScroll(this.$refs.aaaa, {});
  },
  //在mounted阶段，对浏览器来说，已经完成了dom与css规则树的render，并完成对render tree进行了布局，而浏览器收到这一指令，调用渲染器的paint（）在屏幕上显示，而对于vue来说，在mounted阶段，vue的template成功挂载在$el中，此时一个完整的页面已经能够显示在浏览器中，所以在这个阶段，即可以调用节点了
  mounted() {
    this.scroll = new BScroll(document.querySelector(".wrapper"), {
      probeType: 3,
      pullUpLoad: true
    });
    this.scroll.on("scroll", position => {
      console.log(position);
    });
    this.scroll.on("pullingUp", () => {
      console.log("上拉加载更多");
    });
  },
  methods: {
    btnClick() {
      console.log("btnClick");
    }
  }
};
</script>

<style scoped>
.wrapper {
  height: 150px;
  background-color: red;
  overflow: hidden;
}
</style>
