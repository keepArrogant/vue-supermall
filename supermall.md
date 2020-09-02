## supermall项目搭建

### 创建项目

- 本次项目通过vue-cli3来创建 `vue create supermall`，采用默认配置

### 把项目部署到GitHub上

- 进入GitHub官网，输入账号密码进入个人主页，然后创建新的项目，一同默认，出现创建成功之后，进入本地创建项目的命令行中输入：`git remote add origin https://github.com/keepArrogant/testmall.git`,再输入：`git push -u origin master`,然后它会提示你输入github上面的账号密码来验证，输入之后，就提示下面这些说明项目部署到github上成功。

![1597627169815](F:\coderwhy_project\img_note\部署项目到github上.png)

### 项目目录划分

![1597629163782](F:\coderwhy_project\img_note\项目目录划分.png)

- 项目目录主要是细分src里面的，外面几乎不需要动。
  - assets：存放一些资源文件，比如img、css
  - components：存放组件文件，主要是存放公共的组件；里面的common存放其他项目也能使用的组件，content存放只能该项目使用的公共组件。
  - views：存放主要视图的文件夹，在文件夹下单独写某个视图的独有组件
  - router：存放路由相关的文件
  - store：存放Vuex公共状态管理的文件
  - network：存放网络相关的文件
  - common：存放公共的js文件，比如其他文件需要的常量。

### 引入文件

- 引入公共的样式文件： base.css、normalize.css文件到src下的assets文件夹中
- 引入默认的代码风格的设置文件.editorconfig 这个文件在脚手架2中默认会添加，但是在脚手架3种需要自行配置，这对于之后多人合作开发有着至关重要的作用。所以我们直接在脚手架2的项目中复制过来放到项目根目录下就可以了。

### 给文件配置别名

- 因为脚手架3的配置文件默认都隐藏起来了，所以我们之间新建一个文件vue.config.js在项目的根目录下。配置之后在调用某个文件夹的某个内容的时候不用再../这样麻烦了。

```javascript
module.exports = {
  configurewebpack: {
    resolve: {
      alias: {
        'assets': '@/assets',
        'common': '@/common',
        'components': '@/components',
        'network': '@/network',
        'views': '@/views'
      }
    }
  }
}
```

### 引入tabbar模块和项目模块划分

- 在之前的练习中我们把tabbar模块封装好了，现在就可以派上用场了。
- 把之前的tabbar项目打开，找到src文件夹下的components文件夹，把里面的tabbar文件夹和mainTabbar文件夹分别放置到现在项目下的components下的common文件夹和content文件夹。 tabbar文件夹的TabBar.vue和TabBarItem.vue 是可以被其他项目拿去重复利用的，所以放在了 common文件夹；而mainTabbar文件夹的内容只有对当前的项目可适用，所以放在了content文件夹里。

![1597656254108](F:\coderwhy_project\img_note\导入tabbar模块目录划分.png)

- 除了导入组件模块的内容还需要组件里的资源图片，所以把img文件夹的图片导入到assets文件夹

- 之后就对该项目修改路径和导入组件模块。

  - MainTabBar.vue里的图片路径和子组件路径。因为之前配置了别名，所以在图片路径前加上~和别名就好了。

    ```vue
    <!--导入图片太多，在此只展示一个 -->
    <tab-bar>
        <tab-bar-item path="/home" activeColor="blue">
          <img slot="item-icon" src="~assets/img/tabbar/home.svg" />
     <img slot="item-icon-active" src="~assets/img/tabbar/home_active.svg" />
          <div slot="item-text">首页</div>
        </tab-bar-item>
         
    <script>
    //在这里导入文件的方式使用了别名
    import TabBar from "components/common/tabbar/TabBar";
    import TabBarItem from "components/common/tabbar/TabBarItem";
    
    export default {
      name: "MainTabBar",
      components: { TabBar, TabBarItem }
    };
    </script>     
    ```

- 然后就配置相应的路由模块，之前tabbar项目中四个底边按钮可以点击跳转不同的板块，所以这里也要配置。 在本项目中也是在router文件夹里创建index.js文件

  - 由于该项目开始创建时没有勾选安装vue-router，所以在此手动安装

    `npm install vue-router@3.0.2 --save`

- 配置路由文件index.js

  ```javascript
  import Vue from "vue";
  import VueRouter from "vue-router";
  
  //懒加载的方式导入组件
  const Home = () => import('views/home/Home')
  const Category = () => import('views/category/Category')
  const Cart = () => import('views/cart/Cart')
  const Profile = () => import('views/profile/Profile')
  
  // 1.安装插件
  Vue.use(VueRouter);
  
  // 2.创建router
  const routes = [{
    path: '',
    redirect: '/home'
  }, {
    path: '/home',
    component: Home
  }, {
    path: '/category',
    component: Category
  }, {
    path: '/cart',
    component: Cart
  }, {
    path: '/profile',
    component: Profile
  }];
  
  const router = new VueRouter({
    routes,
    mode: "history"
  });
  
  export default router;
  ```

- 在main.js中引入上面配置好的路由文件index.js

  ```javascript
  import Vue from 'vue'
  import App from './App.vue'
  import router from './router'
  
  Vue.config.productionTip = false
  
  new Vue({
    render: h => h(App),
    router
  }).$mount('#app')
  ```

- 在App.vue组件中引入封装好的MainTabBar.vue组件，并使用它

  ```vue
  <template>
    <div id="app">
      <router-view></router-view>
      <main-tab-bar></main-tab-bar>
    </div>
  </template>
  
  <script>
  import MainTabBar from "components/content/mainTabbar/MainTabBar";
  
  export default {
    name: "App",
    components: {
      MainTabBar
    }
  };
  </script>
  
  <style>
  @import "assets/css/base.css";
  </style>
  ```

- 在这里我们使用的是vue-cli3，所以使用`npm run serve`启动服务

![1597658966092](F:\coderwhy_project\img_note\导入tabbar模块启动项目初步样子.png)

### 修改项目页面图标

- 在上面的图片中可以看见，我们项目的页面标题图标是vue官网的图标，现在我们要替换它。

- 存放标题图标的文件在项目的public的idnex.html页面中，在这个文件的头部link标签的href属性中显示存放图标的位置。我们替换这个图标就可以了。

  ```html
  <link rel="icon" href="<%= BASE_URL %>favicon.ico">
  ```

  - 里面的BASE_URL代表当前文件所在的文件夹，图标也是在这个文件夹里面。

### 添加首页头部组件和样式

- 上面把项目的底部和路由配置好了，现在需要对每个页面实现头部样式，可以发现头部样式高度都是一致的，只是其中的内容不同而已，我们可以在这里使用组件插槽来替换其中的内容。

- 在components文件夹下的common文件夹新建navbar文件夹和NavBar.vue组件，这个组件是公共的头部组件

  ```vue
  <template>
    <div class="nav-bar">
      <div class="left"><slot name="left"></slot></div>
      <div class="center"><slot name="center"></slot></div>
      <div class="right"><slot name="right"></slot></div>
    </div>
  </template>
  
  <script>
  export default {
    name: "NavBar"
  };
  </script>
  
  <style>
  .nav-bar {
    display: flex;
    line-height: 44px;
    height: 44px;
    text-align: center;
    box-shadow: 0 1px 1px rgba(100, 100, 100, 0.1);
  }
  .left,
  .right {
    width: 60px;
  }
  .center {
    flex: 1;
  }
  </style>
  ```

- 公共的头部文件配置好了，我们需要在首页中使用它 ，我们来到Home.vue组件来引入它

  - 首页头部只使用了NavBar组件的第二个插槽。

  ```vue
  <template>
    <div id="home">
      <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    </div>
  </template>
  
  <script>
  import NavBar from "components/common/navbar/NavBar";
  export default {
    name: "Home",
    components: {
      NavBar
    }
  };
  </script>
  
  <style>
  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
  }
  </style>
  ```

### 首页数据的请求

- 在之前的学习中我们封装了axios框架的网络请求和响应，首先我们导入这个文件到项目的network文件夹中
- 使用axios框架务必要先下载它 `npm install axios@0.18.0 --save`
- 因为项目当中会有很多的网络请求，而且每个组件都会有各种各样的请求，所以我们要把这些分开，所以对于某个组件来建立一个js文件存放网络请求和响应的代码。
- 我们现在建立的是Home组件的网络请求，所以在network文件夹下新建home.js文件

 ```javascript
//引入封装好的axios文件
import {
  request
} from './request'
//设置请求方法和url
export function getHomeMultidata() {
  return request({
    url: '/home/multidata'
  })
}
 ```

- 在Home组件中使用它

```vue
<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";
import { getHomeMultidata } from "network/home";

export default {
  name: "Home",
  components: {
    NavBar
  },
  data() {
    return {
      banners: [],
      recommends: []
    };
  },
  created() {
    // 1.请求多个数据
    getHomeMultidata().then(res => {
      // this.result = res;
      this.banners = res.data.banner;
      this.recommends = res.data.recommend;
    });
  }
};
</script> <!-- 重复的css代码就不展示了-->
```

- 重启服务器 `npm run serve` 输入网址 http://localhost:8080/
- 跳转到首页，打开Vue插件 可以看到数据被获取到Home组件中。

![1597670528577](F:\coderwhy_project\img_note\初步获取数据到Home组件中.png)

### 导入封装好的轮播图

- 顶部和底部的组件都弄好了， 我们把轮播图的组件导入进来，因为封装的轮播图可以用于很多项目，所以把它放到components文件夹下的common文件夹中。

- 封装好的轮播图文件有Swper.vue和SwiperItem.vue还有导出它们俩的index.js 所以我们在使用这个组件时，只需要导入index.js文件就可以了。

- 因为轮播图要显示在Home组件中，所以把组件引入到Home组件里面就可以了，但是要注意，Home组件里我们为了看着简洁和易于管理，我们在views文件夹的home文件夹中新建childComps文件夹，里面存放该项目具体的轮播图组件的实现，然后把它导入到Home组件就好了。

  ```vue
  <!-- HomeSwiper.vue 它使用轮播图组件，所以要先导入封装轮播图的index.js文件，再把两个具体组件导入并使用，最后该文件要导入Home组件中，数据也要传递过去，所以用到了props，把banners数据传递到HomeSwiper组件并展示出来。-->
  <template>
    <swiper>
      <swiper-item v-for="item in banners" :key="item-index">
        <a :href="item.link">
          <img :src="item.image" alt />
        </a>
      </swiper-item>
    </swiper>
  </template>
  
  <script>
  import { Swiper, SwiperItem } from "components/common/swiper";
  
  export default {
    name: "HomeSwiper",
    props: {
      banners: {
        type: Array,
        default() {
          return [];
        }
      }
    },
    components: {
      Swiper,
      SwiperItem
    }
  };
  </script>
  
  <style>
  </style>
  ```

- 把HomeSwiper.vue组件导入到Home.vue中

  ```vue
  <template>
    <div id="home">
      <nav-bar class="home-nav">
        <div slot="center">购物街</div>
      </nav-bar>
      <home-swiper :banners="banners"></home-swiper>
    </div>
  </template>
  
  <script>
  import NavBar from "components/common/navbar/NavBar";
  import HomeSwiper from "./childComps/HomeSwiper";
  
  import { getHomeMultidata } from "network/home";
  
  export default {
    name: "Home",
    components: {
      NavBar,
      HomeSwiper
    },
    data() {
      return {
        banners: [],
        recommends: []
      };
    },
    created() {
      // 1.请求多个数据
      getHomeMultidata().then(res => {
        // this.result = res;
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    }
  };
  </script>
  ```

- 重启服务器，打开网页，可以看见轮播图可以正常显示。

  ![1597715838865](F:\coderwhy_project\img_note\导入轮播图之后的展示效果.png)

### 推荐信息的展示

- 推荐信息的板块也是在Home组件里，所以我们跟上面的轮播图一样，在views的home的childComps文件夹里新建RecommendView组件，用于展示推荐信息。

- 拿到Home组件获取到的链接、图片、标题数据 ，双向绑定数据和修改样式信息

  ```vue
  <!-- RecommendView.vue-->
  <template>
    <div class="recommend">
      <div v-for="item in recommends" class="recommend-item" :key="item">
        <a :href="item.link">
          <img :src="item.image" alt />
          <div>{{item.title}}</div>
        </a>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "RecommendView",
    props: {
      recommends: {
        type: Array,
        default() {
          return [];
        }
      }
    }
  };
  </script>
  
  <style>
  .recommend {
    display: flex;
    width: 100%;
    text-align: center;
    font-size: 12px;
  
    padding: 10px 0 20px;
    border-bottom: 10px solid #eee;
  }
  .recommend-item {
    flex: 1;
  }
  .recommend-item img {
    width: 70px;
    height: 70px;
    margin-bottom: 10px;
  }
  </style>
  ```

- 在Home组件中引入RecommendView组件

```vue
<template>
  <div id="home">
   <!-- 重复部分就不展示了-->
    <recommend-view :recommends="recommends"></recommend-view>
  </div>
</template>
<script>
//重复部分就不展示了
import RecommendView from "./childComps/RecommendView";

export default {
  name: "Home",
  components: {
    NavBar,
    HomeSwiper,
    RecommendView
  },
  data() {
    return {
      banners: [],
      recommends: []
    };
  },
  created() {
    // 1.请求多个数据
    getHomeMultidata().then(res => {
      // this.result = res;
      this.banners = res.data.banner.list;
      this.recommends = res.data.recommend.list;
    });
  }
};
</script>
   <!-- 重复部分就不展示了-->
```

### 首页本周流行板块组件的封装

- 本周流行模块组件中是有链接的一整张图片，所以写组件很简单
- 首先它是在首页里面的，所以在views文件夹下的home文件夹下的childComps文件夹下新建FeatureView组件用于展示本周流行。

- 由于图片资源放置在assets资源文件夹中，所以直接获取

```vue
<template>
  <div class="feature">
    <a href="http://act.mogujie.com/zzlx67">
      <img src="~assets/img/home/recommend_bg.jpg" alt />
    </a>
  </div>
</template>

<script>
export default {};
</script>

<style>
.feature img {
  width: 100%;
}
</style>
```

- Home组件中去使用FeatureView组件。

```vue
<template>
<!-- 重复部分就不展示了-->
<div id="home">
    <nav-bar class="home-nav">
        <div slot="center">购物街</div>
    </nav-bar>
    <feature-view></feature-view>
</div>
</template>

<script>
import FeatureView from "./childComps/FeatureView";
export default {
  name: "Home",
  components: {
    FeatureView
     }
}
</script> 
<!-- 在展示效果的时候会发现本周流行的下半部分有一些是看不见的，我们就把navbar模块也就是头部使用固定定位定住。-->
<style>
#home {
  padding-top: 44px;
}
.home-nav {
  background-color: var(--color-tint);
  color: #fff;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}
</style>

```

![1597739834725](F:\coderwhy_project\img_note\本周流行展示效果.png)

### TabControl的封装

- 接下来依然是在Home组件中插入其他组件，但是这个组件有点特殊，它在其他的组件中也要使用，所以我们来封装一下。
- 由于该组件在本项目中用的可能会多一些，所以我们把它放置在components文件夹下的content文件夹中，在这里新建tabControl文件夹，再在它里面建立组件TabControl

```vue
<!--TabControl.vue -->
<template>
  <div class="tab-control">
    <div
      v-for="(item, index) in titles"
      :key="item"
      class="tab-control-item"
      :class="{active: index === currentIndex}"
      @click="itemClick(index)"
    >
      <span>{{item}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "TabControl",
  props: {
    titles: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  data() {
    return {
      currentIndex: 0
    };
  },
  methods: {
    itemClick(index) { //该方法用作判断是否点击了，点击就把对应的index索引传递给变量，变量又传递给对应的 active 用来改变样式
      this.currentIndex = index;
    }
  }
};
</script>

<style>
.tab-control {
  display: flex;
  text-align: center;
  font-size: 15px;
  height: 40px;
  line-height: 40px;
  background-color: #fff;
}
.tab-control-item {
  flex: 1;
}
.tab-control-item span {
  padding: 5px;
}
.active {
  color: var(--color-high-text);
}
.active span {
  border-bottom: 3px solid var(--color-tint);
}
</style>
```

- 在Home组件中使用TabControl组件，并且把数据传递给TabControl组件

```vue
<template>
  <div id="home">
      <tab-control class="tab-control" :titles="['流行', '新款', '精选']"></tab-control>
  </div>
</template>   

<script>
import TabControl from "components/content/tabControl/TabControl";
</script>

<style>   
.tab-control {
  position: sticky;  <!-- 在移动端下可以设置该粘性定位，如果它距离顶部44px时就把定位自动改为固定定位 实现了简单的滑动固定-->
  top: 44px;
}
</style>    
```

![1597807247043](F:\coderwhy_project\img_note\TabControl组件封装并在Home组件中使用效果.png)

### 首页数据的请求和保存

- 做到现在，我们需要把页面上的流行、新款、精选中的商品数据都获取到并且保存到data里面，涉及到获取数据那么我们要去network文件夹下的home.js文件中设置获取数据路径

```javascript
//home.js
export function getHomeGoods(type, page) {
  return request({
    url: '/home/data',
    params: {
      type,
      page
    }
  })
```

- 获取之后导入该方法并且把数据保存到Home组件的data数据中

```vue
<script>
import { getHomeMultidata, getHomeGoods } from "network/home";
export default {
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] }
      }
    };
  },
  created() { //在创建Home组件时就调用其中的方法，而方法具体内容都写到了methods中
    // 1.请求多个数据
    this.getHomeMultidata();

    // 2.请求商品数据
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");
  },
  methods: {
    getHomeMultidata() {
      getHomeMultidata().then(res => {
        // this.result = res;
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },
    getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then(res => {
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1;
      });
    }
  }
};   
</script>    
```

![1597849603116](F:\coderwhy_project\img_note\获取首页的数据并保存.png)

- 可以看见右下角数据已经被获取并保存了。

### 首页商品数据的展示

- 在上面的步骤中我们拿到了首页的数据，所以现在我们要建立组件来展示获取到的数据。
- 由于该组件只适合在本项目下使用，所以在components文件夹下的content文件夹下新建goods文件夹，在里面创建两个组件分别为：GoodsList.vue 、GoodsListItem.vue

- 由于获取到的数据在Home组件里，所以我们要把数据传递给GoodsList组件，再传递给它的子组件GoodsListItem

```vue
<!-- Home.vue  把获取到的goods中'pop'里的list数据传递给子组件的goods数据中-->
<goods-list :goods="goods['pop'].list"></goods-list>

<script>
import GoodsList from "components/content/goods/GoodsList";
 export default {
     ...,
    data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] }
      }
    };
  }
};
</script>    
```



```vue
<!-- GoodsList.vue 通过:goods获取Home组件传递过来的数据，通过v-for遍历这整个大数据，然后把详细数据传递给子组件的goods-item中-->
<template>
  <div class="goods">
    <goods-list-item v-for="item in goods" :key="item.value" :goods-item="item"></goods-list-item>
  </div>
</template>

<script>
import GoodsListItem from "./GoodsListItem";
export default {
  name: "GoodsList",
  props: {
    goods: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  components: {
    GoodsListItem
  }
};
</script>

<style>
.goods {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;

  padding: 2px;
}
</style>
```

```vue
<!-- GoodsListItem.vue 通过goodsItem接收父组件传递过来的数据-->
<template>
  <div class="goods-item">
    <img :src="goodsItem.show.img" alt />
    <div class="goods-info">
      <p>{{goodsItem.title}}</p>
      <span class="price">{{goodsItem.price}}</span>
      <span class="collect">{{goodsItem.cfav}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "GoodsListItem",
  props: {
    goodsItem: {
      type: Object,
      default() {
        return [];
      }
    }
  }
};
</script>

<style>
.goods-item {
  padding-bottom: 40px;
  position: relative;

  width: 48%;
}
.goods-item img {
  width: 100%;
  border-radius: 5px;
}
.goods-info {
  font-size: 12px;
  position: absolute;
  bottom: 5px;
  left: 0;
  right: 0;
  overflow: hidden;
  text-align: center;
}
.goods-info p {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  margin-bottom: 3px;
}
.goods-info .price {
  color: var(--color-hight-text);
  margin-right: 20px;
}
.goods-info .collect {
  position: relative;
}
.goods-info .collect::before {
  content: "";
  position: absolute;
  left: -15px;
  top: -1px;
  width: 14px;
  height: 14px;
  background: url("~assets/img/common/collect.svg");
}
</style>
```

![1597892319596](F:\coderwhy_project\img_note\首页数据的展示.png)

### 首页实现点击切换商品

- 由于之前的点击显示高亮的三个span都是在TabControl组件中，所以我们现在要想点击切换商品，展示数据，就得把点击的参数传递给Home组件；从父传子props，从子传父$emit

```vue
<!-- TabControl.vue-->
<template>
  <div class="tab-control">
    <div
      v-for="(item, index) in titles"
      :key="'tab-control-'+item"
      class="tab-control-item"
      :class="{active: index === currentIndex}"
      @click="itemClick(index)"
    >
      <span>{{item}}</span>
    </div>
  </div>
</template>

<script>
  methods: {
    itemClick(index) {
      this.currentIndex = index;
      this.$emit("tabClick", index);
    }
  }
```

- 通过$emit传递一个taClick方法

```vue
<!-- Home.vue  获取子组件传递过来的tabClick方法 通过index索引判断点击获取数据  showGoods方法在计算属性computed里 实现数据展示-->
<template>
  <div id="home">
<tab-control class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
   </div>
</template>  

<script>
    data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] }
      },
      currentType: "pop"
    };
  },
    computed: {
    showGoods() {
      return this.goods[this.currentType].list;
    }
  },
methods: {
    /**
     * 事件监听相关的方法
     */
    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = "pop";
          break;
        case 1:
          this.currentType = "new";
          break;
        case 2:
          this.currentType = "sell";
          break;
      }
    }      
```

![1597894842579](F:\coderwhy_project\img_note\首页商品的切换展示.png)

### Better-scroll的安装和使用

- 在浏览器中我们使用手机模式来浏览滑动页面感觉挺顺滑的，但是在手机上就不见得了。现在我们需要把分类组件的假数据用一个盒子装住，让其实现顺滑下拉而且到最底部有弹性的效果，于是我们要用到Better-scroll第三方模块
- 下载安装： `npm install better-scroll@1.13.2 --save`
- 在分类组件 也就是 Category.vue组件中导入使用

```vue
<template>
  <div class="wrapper" ref="aaaa">
    <ul class="content">
      <li>分类商品1</li>
      <li>分类商品2</li> 
        ...
    </ul>
  </div>
</template>


<script>
import BScroll from "better-scorll";

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
  mounted(){
    // console.log(this.$refs.aaaa)
    // console.log(document.querySelector('.wrapper'))
    this.scroll = new BScroll(document.querySelector('.wrapper'),{})
  }
};
</script>

<style>
.wrapper {
  height: 150px;
  background-color: red;
  overflow: hidden;
}
</style>
```

![1597916428838](F:\coderwhy_project\img_note\better-scroll的安装使用并展示效果.png)

### Better-scroll在Vue项目中的使用

- 还是在Category.vue组件中使用
- 我们要操作元素，必须把方法写在mounted属性里。

```vue
<template>
  <div class="wrapper" ref="aaaa">
    <ul class="content">
      <button @click="btnClick">点击</button>
      <li>分类商品1</li>
        ....
    </ul>
  </div>
</template>

<script>
import BScroll from "better-scroll";
 export default { 
   mounted() {
    this.scroll = new BScroll(document.querySelector(".wrapper"), {
      probeType: 3, //监听滚轮 有 0 1 2 3 各种状态
      pullUpLoad: true
    });
    this.scroll.on("scroll", position => { //打印页面滚动的坐标
      console.log(position);
    });
    this.scroll.on("pullingUp", () => {//到达底部上拉时候打印
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
```

### Better-Scroll的封装和在首页中的使用

- 如果直接引入Better-Scroll第三方框架到每一个大的组件中使用，这样会有很强的模块依赖性，不利于后期更改维护，如果这个第三方模块不再维护了我们要换成其他的第三方模块时修改起来也很麻烦，所以在使用第三方模块时要有很强的模块封装思想，需要换模块的时候只需要更改封装的js代码即可。
- 由于封装好的Better-Scroll第三方模块可以在其他项目中使用，所以我们把封装的文件放置在components文件夹下的common文件夹下的scroll文件夹，创建Scroll.vue组件。其他组件直接调用使用就可以了。

```vue
<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import BScroll from "better-scroll";

export default {
  name: "Scroll",
  data() {
    return {
      scroll: null
    };
  },
  mounted() {
      //因为这里是封装之后别个组件拿去使用，我们通过获取DOM会出现重名出错的现象。
      //想要获取到组件的wrapper 我们使用ref进行绑定；
      //ref如果是绑定在组件中的，那么通过this.$refs.refname获取到的是一个组件对象
      //ref如果是绑定在普通的元素中，那么通过this.$refs.refname获取到的是一个元素对象
    this.scroll = new BScroll(this.$refs.wrapper, {});
  }
};
</script>

<style scoped>
</style>
```

- 封装之后我们在Home组件中使用它;首先scroll使用时要获取class为wrapper的父容器，在父容器中建立class名为content的可以滚动的区域。我们想要滚动的是Home组件除去上下固定组件的内容 =>把要滚动的区域放置在scroll标签中，scroll的父级加class=“wrapper”，然后调整他们的样式

```vue
<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <scroll class="content">
      <home-swiper :banners="banners"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view></feature-view>
      <tab-control class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
  </div>
</template>

<script>
import Scroll from "components/common/scroll/Scroll";
</script>

<style scoped>
#home {
  padding-top: 44px;
  height: 100vh;
  position: relative;
}
 ....
.content {
  overflow: hidden;
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}
</style>    
```

- 就可以看见首页实现了非常顺滑的滚动效果，还有弹性的拉伸效果。

### BackTop组件的封装和使用

- 市面上很多软件在浏览东西的时候，会有浏览单一定内容的情况下右边会有个小按钮或者图标点击可以返回顶部，为了在本项目中实现该功能，我们需要创建新的组件并封装它，因为它在需要页面都用得上。

- 该功能适用于该项目下，所以在components文件夹下的content文件夹下新建backTop文件夹，在里面创建BackTop.vue组件

  ```vue
  <!-- BackTop.vue-->
  <template>
    <div class="back-top">
      <img src="~assets/img/common/top.png" alt />
    </div>
  </template>
  
  <script>
  export default {
    name: "BackTop"
  };
  </script>
  
  <style scoped>
  .back-top {
    position: fixed;
    right: 8px;
    bottom: 55px;
  }
  .back-top img {
    width: 43px;
    height: 43px;
  }
  </style>
  ```

- 试着在Home组件中使用它。它是固定在一个区域里，不随着scroll滚动，所以和scroll是兄弟组件关系；我们要获取滚动之后，点击回滚到顶部的高度，把高度改为0；如果back-top和scroll传递数据会很麻烦，我们直接在Home组件中给back-top绑定点击事件，并且获取scroll对象并操作高度，这样比在home组件中获取scroll滚动高度传递给back-top中操作要少个步骤。

```vue
<!-- Scroll.vue -->
<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import BScroll from "better-scroll";

export default {
  name: "Scroll",
  data() {
    return {
      scroll: null
    };
  },
  mounted() {
    this.scroll = new BScroll(this.$refs.wrapper, {
      click: true
    });
    this.scroll.scrollTo(0, 0); //默认方法
  },
  methods: {
    scrollTo(x, y, time = 300) {
      this.scroll.scrollTo(x, y, time); //也可以自行定义高度和时间
    }
  }
};
</script>

<style scoped>
</style>
```

```vue
<!-- Home.vue 修饰.native什么时候使用：在我们需要监听一个组件的原生事件时，必须给对应的事件加上.native修饰符，才能进行监听-->
<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <scroll class="content" ref="scroll">
      <home-swiper :banners="banners"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view></feature-view>
      <tab-control class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backClick"></back-top>
  </div>
</template>

<script>
import Scroll from "components/common/scroll/Scroll";
import BackTop from "components/content/backTop/BackTop";
export default {
    ...,
 methods: {   
    backClick() {
      this.$refs.scroll.scrollTo(0, 0);
    },
  }
};
</script>
```

![1597996014414](F:\coderwhy_project\img_note\backtop组件的封装和使用并与scroll交互滚动高度.png)

### BackTop组件的展示与隐藏

- 我们要实现页面滚动到一定高度时，BackTop组件展示，然后点击回到顶部并隐藏该组件。上面我们封装了该组件，实现了点击回到顶部的效果，但是没有实现展示与隐藏的效果。
- 要监听实现该效果，必须实时知道滚轮滚动到了多少高度，所以我们在封装好的Scoll组件中设置动态监听该滚动。因为有的组件使用Scroll组件时需要监听滚动，有的又不需要，所以我们得弄个动态设置。

```vue
<!-- Scroll.vue-->
<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import BScroll from "better-scroll";

export default {
  name: "Scroll",
  props: {
    probeType: {//默认时候为0，表示不监听滚动
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      scroll: null
    };
  },
  mounted() {
    // 1.创建BScroll对象
    this.scroll = new BScroll(this.$refs.wrapper, {
      click: true,
      probeType: this.probeType
    });
    // 2.监听滚动的位置
    this.scroll.on("scroll", position => {
      // console.log(position);
      this.$emit("scroll", position); //发送scroll
    });
  },
  methods: {
    scrollTo(x, y, time = 300) {
      this.scroll.scrollTo(x, y, time);
    }
  }
};
</script>

<style scoped>
</style>
```

- 在Home组件中获取scroll组件对象，设置probeType值为3，实时监听scroll对象滚动坐标。back-top的显示与隐藏使用v-show指令，值为定义的参数，通过监听滚动的高度下拉达到1000就显示

```vue
<!-- Home.vue-->
<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <home-swiper :banners="banners"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view></feature-view>
      <tab-control class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>
  </div>
</template>

<script>
import Scroll from "components/common/scroll/Scroll";
import BackTop from "components/content/backTop/BackTop";
export default {
    ...
  data() {
    return {
    isShowBackTop: false
    };
  },
  methods: {
    contentScroll(position) {
    // console.log(position) //打印滚动坐标
    this.isShowBackTop = -position.y > 1000;
    },
        ...
}
</script>
```

- 再打开就会实现下拉1000高度BackTop组件就会显示出来，点击回顶部或者上滑高度小于1000 BackTop组件就隐藏。

### 完成上拉加载更多

- 在Home组件的三个板块中，获得到的商品信息每一页只有三十个，我们现在要实现下拉到商品的底部时，加载该板块下的下一页的数据，两页的数据还是可以上下翻滚。
- 要实现上拉加载更多，我们肯定要去操作Scroll组件，给它富有pullUpLoad属性，该属性为true时，就监听上拉事件。该组件我们封装好了，其他组件会拿去使用的，所以有些组件不需要监听上拉事件，我们就没必要把该属性的值写死。

```vue
<!-- Scroll.vue-->
<script>
import BScroll from "better-scroll";

export default {
  name: "Scroll",
  props: {
    probeType: {
      type: Number,
      default: 0
    },
    pullUpLoad: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      scroll: null
    };
  },
  mounted() {
    // 1.创建BScroll对象
    this.scroll = new BScroll(this.$refs.wrapper, {
      click: true,
      probeType: this.probeType,
      pullUpLoad: this.pullUpLoad
    });
    // 2.监听滚动的位置
    this.scroll.on("scroll", position => {
      // console.log(position);
      this.$emit("scroll", position); //发送scroll
    });

    // 3.监听上拉事件
    this.scroll.on("pullingUp", () => {
      this.$emit("pullingUp"); //把pullingUp事件传递给父组件
    });
  },
  methods: {
    scrollTo(x, y, time = 300) {
      this.scroll.scrollTo(x, y, time);
    },
    finishPullUp() {
      this.scroll.finishPullUp();
    }
  }
};
</script>

<style scoped>
</style>
```

- 在Home组件中接收Scroll组件传递过来的pullingUp事件，使用自定义方法来处理它。

```vue
<!-- Home.vue-->
<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <scroll
      class="content"
      ref="scroll"
      :probe-type="3"
      @scroll="contentScroll"
      :pull-up-load="true"
      @pullingUp="loadMore"
    >
      <home-swiper :banners="banners"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view></feature-view>
      <tab-control class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
  </div>
</template>   

<script>
import Scroll from "components/common/scroll/Scroll"; 
export default {
    ...,
    methods: {
    loadMore() {
      //调用getHomeGoods方法，传递当前点击的板块
      this.getHomeGoods(this.currentType);
      //拿到scroll对象调用刷新，重新计算区域要滚动的高度
      // this.$refs.scroll.srcoll.refresh();
    },
    getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then(res => {
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1;
        //调用scroll对象的finishPullUp方法
        this.$refs.scroll.finishPullUp();
      });
    }
  }
};
</script>
```

- 当我们上拉显示商品的时候，到达当前默认页面的底部时，底部会回弹一下，加载出下一页商品的数据。

### 解决首页中Bertter-Scroll可滚动区域的问题

- 我们在打开首页往下面拉浏览商品时，有时候会出现下拉无效，要重复下拉才能继续浏览的情况
- 为了解决这个情况我们先把上面写的上拉加载更多的代码删除掉。

- Better-Scroll在决定有多少区域可以滚动时，是根据scrollerHeight属性决定的
  - scrollerHeight属性是 根据Better-Scroll的content中的子组件的高度。
  - 但是我们的首页中，刚开始在计算scrollerHeight属性时，是没有将图片计算在内的。
  - 后来图片加载进来之后有了新的高度，但是scrollerHeight属性并没有进行更新
  - 所以滚动出现了问题。
- 如何解决这个问题呢？
  - 监听每一张图片是否加载完成了，只要有一张图片加载完成了，执行一次refresh()
  - 如何监听图片加载完成了？
    - 原生的js监听图片：img.onload = function(){}
    - Vue中监听：@load=“方法”
  - 调用scroll的refresh()
- 首先，监听图片，要修改GoodsListItem.vue组件

```vue
<template>
  <div class="goods-item">
    <img :src="goodsItem.show.img" @load="imageLoad" />
    ...
  </div>
</template>

<script>
export default {
    ...,
  methods: {
    imageLoad() {
      this.$bus.$emit("itemImageLoad"); //监听图片，创建方法传递事件
    }
  }
};
</script>
```

- Home组件接收GoodsListItem组件传递过来的事件，完成该事件时调用Scroll组件的refresh方法

```vue
<script>
export default {
    ...,
    created() {//创建Home组件时就调用该方法
        // 3. 监听item中图片加载完成
    	this.$bus.$on("itemImageLoad", () => {
      		console.log("-------");
      		this.$refs.scroll.refresh();
    });
  },
};
</script>
```

- 在上面的itemImageLoad事件中调用了Scroll组件的refresh方法，我们来编写它

```vue
<script>
   export default {
       ...,
methods: {
       refresh() {
      	this.scroll.refresh();
    }
   }
};
```

- 在上面传递事件和调用事件中都用到了$bus，实际上它是一个Vue实例，挂载vue.prototype上，创建它之后我们就可以使用$refs和$emit传递事件了。于是我们要在main.js主文件中创建。

```javascript
... //bus 事件总线
Vue.prototype.$bus = new Vue()
...
```

- 再次打开网页，滑动浏览商品的时候就不会出现划不动的卡顿情况。

### refresh函数找不到的bug处理

- 在我们进入首页的时候，有时候图片加载太快就会出现refresh not defined这样的报错问题
- 这是因为我们的调用refresh的函数放置在了created属性里，在这个属性里的代码是在组件刚生成的时候就立即执行的，所以scroll组件有可能还没挂载到home组件上就被执行里面的方法自然就报错了。所以我们要把调用scroll组件的代码放置到mounted属性上，在子组件scroll被加载完毕时执行里面的代码，这时就不会出现错误了；
- 我们还要对scroll组件里的methods属性里的方法做二次判断，这样更加严谨不容易报错。

```vue
<!-- Scroll.vue-->
<script>
methods: {
    scrollTo(x, y, time = 300) {
      //为了防止图片资源加载过快而scroll组件还没挂载到Home组件报错而做的判断
      this.scroll && this.scroll.scrollTo(x, y, time);
    },
    finishPullUp() {
      this.scroll && this.scroll.finishPullUp();
    },
    refresh() {
      this.scroll && this.scroll.refresh();
    }
  }
```

```vue
<!-- Home.vue-->
<script> 
mounted() {
    // 3. 监听item中图片加载完成  如果写在created属性里面scroll可能还没挂载到Home组件上
    this.$bus.$on("itemImageLoad", () => {
      console.log("-------");
      this.$refs.scroll && this.$refs.scroll.refresh();
    });
  },
```

### 刷新频繁的防抖函数处理

- 在首页获取商品图片数据时候会执行scroll组件的refresh方法，加载一张图片就执行一次，这样拖慢了代码的执行速度，所以我们在这里使用 防抖debounce/节流throttle
- 防抖函数起作用的过程
  - 如果我们直接执行refresh，那么refresh函数会被执行30次
  - 可以将refresh函数传入到debounce函数中，生成一个新的函数。
  - 之后在调用非常频繁的时候，就使用新生成的函数。
  - 而新生成的函数，并不会非常频繁的使用，如果下一次执行来的非常快，那么会将上一次取消掉

- 我们把防抖函数放置在Home组件的methods属性中

  ```javascript
      debounce(func, delay) {
        let timer = null;
        return function(...args) {
          if (timer) clearTimeout(timer);
          timer = setTimeout(() => {
            func.apply(this, args);
          }, delay);
        };
      },
  ```

- 然后在mounted属性中调用防抖函数

```javascript
mounted() {
    // 3. 监听item中图片加载完成  如果写在created属性里面scroll可能还没挂载到Home组件上
    const refresh = this.debounce(this.$refs.scroll.refresh, 50);
    this.$bus.$on("itemImageLoad", () => {
      refresh();
      // this.$refs.scroll && this.$refs.scroll.refresh();
    });
  },
```

- debounce防抖函数传递的第二个参数是设置的时间

### 封装防抖函数

- debounce函数可能在许多数据加载的地方用到，所以我把它封装起来，方便复用

- 在项目的scr文件夹下的common文件夹下新建utils.js文件

```javascript
export function debounce(func, delay) {
  let timer = null;
  return function (...args) {
    if (timer) clearTimeout(timer);
    timer = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}
```

- 需要使用防抖函数的组件就直接导入该文件就行了。

- 下面在Home组件中使用

```javascript
//Home.vue
//导入debounce函数
import { debounce } from "common/utils";
//使用该函数
const refresh = debounce(this.$refs.scroll.refresh, 50);
    this.$bus.$on("itemImageLoad", () => {
      refresh();
      // this.$refs.scroll && this.$refs.scroll.refresh();
    });
```

### 完成上拉加载更多

- 在之前处理BUG的时候，我把写的上拉加载更多删除掉了。现在加上。

```vue
<!-- Scroll.vue-->
<script>
export default {
    ...,
    mounted() {
        // 3.监听scroll滚动到底部
        if (this.pullUpLoad) {
          this.scroll.on("pullingUp", () => {
            this.$emit("pullingUp");
          });
        }
      },
  methods: {
      finishPullUp() {
      this.scroll && this.scroll.finishPullUp();
   	   }
    }
};
</script>
```

```vue
<!-- Home.vue-->
<scroll
      class="content"
      ref="scroll"
      :probe-type="3"
      @scroll="contentScroll"
      :pull-up-load="true"
      @pullingUp="loadMore">....</scroll>

<script>
    ...
      methods: {
        loadMore() {
        this.getHomeGoods(this.currentType)
        },  
        getHomeGoods(type) {
          const page = this.goods[type].page + 1;
          getHomeGoods(type, page).then(res => {
            this.goods[type].list.push(...res.data.list);
            this.goods[type].page += 1;

           // 完成上拉加载更多
        	this.$refs.scroll.finishPullUp()
      	});
      } 
    }
</script>
```

- 打开网页，把商品数据拉到最底部，会回弹一下然后加载出下一页的数据，继续向下滚动，继续加载显示。

### tabControl的吸顶效果

#### 获取到tabControl的offsetTop

- 必须知道滚动到多少时，开始有吸顶效果，这个时候就需要获取tabControl的offsetTop
- 但是，如果直接在mounted中获取tabControl的offsetTop，那么值是不正确的
- 如何获取正确的值？
  - 监听HomeSwiper中img的加载完成
  - 加载完成后，发出事件，在Home.vue中，获取正确的值。
- 补充：
  - 为了不让HomeSwiper多次发出事件
  - 可以使用isLoad的变量进行状态的记录
- 注意：这里不进行多次调用和debounce的区别

```vue
<!--HomeSwiper.vue-->
<template>
  <swiper>
    <swiper-item v-for="item in banners" :key="item.index">
      <a :href="item.link">
        <img :src="item.image" @load="imageLoad" />
      </a>
    </swiper-item>
  </swiper>
</template>

<script>
import { Swiper, SwiperItem } from "components/common/swiper";

export default {
  name: "HomeSwiper",
  props: {
    banners: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  data() {
    return {
      isLoad: false
    };
  },
  components: {
    Swiper,
    SwiperItem
  },
  methods: {
    imageLoad() {
      if (!this.isLoad) {
        this.$emit("swiperImageLoad");
        this.isLoad = true;
      }
    }
  }
};
</script>
```

```vue
<!-- Home.vue-->
<home-swiper :banners="banners" @swiperImageLoad="swiperImageLoad"></home-swiper>
<script>
methods: {
  swiperImageLoad() {
      // 2.获取tabControl的offsetTop
      // 所有的组件都有一个属性$el：用于获取组件中的元素
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop;
    }
};
</script>
```

#### 监听滚动，动态的改变tabControl的样式

- 问题：动态的改变tabControl的样式时，会出现两个问题：
  - 一：下面的商品内容会突然上移
  - 二：tabControl虽然设置了fixed，但是也随着Better-Scroll一起滚出去了
  - 三：在我们点击tabControl组件切换商品时滑动，会发现不跟随点击的高亮。
- 其他方案来解决停留问题
  - 在最上面，多复制一份TabControl组件对象，利用它来实现停留效果
  - 当用户滚动到一定位置时，TabControl显示出来
  - 当用户滚动没有达到一定位置时，TabControl隐藏起来

```vue
<!-- Home.vue-->
<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <tab-control
      :titles="['流行', '新款', '精选']"
      @tabClick="tabClick"
      ref="tabControl1"
      class="tab-control"
      v-show="isTabFixed"
    ></tab-control>
    <scroll
      class="content"
      ref="scroll"
      :probe-type="3"
      @scroll="contentScroll"
      :pull-up-load="true"
      @pullingUp="loadMore"
    >
      <home-swiper :banners="banners" @swiperImageLoad="swiperImageLoad"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view></feature-view>
      <tab-control :titles="['流行', '新款', '精选']" @tabClick="tabClick" ref="tabControl2"></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>
  </div>
</template>

<script>
export default {
    ...,
    data() {
      return {
         ...,
        isShowBackTop: false,
        tabOffsetTop: 0,
        isTabFixed: false
             };
         }, 
          methods: {
            tabClick(index) {
              switch (index) {
                case 0:
                  this.currentType = "pop";
                  break;
                case 1:
                  this.currentType = "new";
                  break;
                case 2:
                  this.currentType = "sell";
                  break;
              }
              this.$refs.tabControl1.currentIndex = index;
              this.$refs.tabControl2.currentIndex = index;
            },  
             contentScroll(position) {
              // console.log(position) //打印滚动坐标
              // 1.判断BackTop是否显示
              this.isShowBackTop = -position.y > 1000;

              // 2.决定tabControl是否吸顶(position: fixed)
              this.isTabFixed = -position.y > this.tabOffsetTop;
            },
          }
       }
</script>

<style scoped>
#home {
  /* padding-top: 44px; */
  height: 100vh;
  position: relative;
}
.home-nav {
  background-color: var(--color-tint);
  color: #fff;
  /* 在使用浏览器原生滚动时，为了让导航不跟随一起滚动 */
  /* position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9; */
}
.tab-control {
  position: relative;
  z-index: 9;
}
.content {
  overflow: hidden;
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}
</style>     
```

- 这样就在我们浏览商品，切换商品继续浏览的时候，会跟随着你点击切换的板块高亮。并且吸顶和不吸顶时，高亮也会继续保持。

![1598238829903](F:\coderwhy_project\img_note\根据滚动高度自动吸顶.png)

### 让Home组件保持原来的状态

- 在我们浏览Home组件中的商品时，点击tab-bar的其他板块了，然后再切换到首页时会出现自动返回至首页顶部了，而不是在刚才浏览的位置。
- 原因是在切换组件时，Home组件被销毁了，为了保持活性，我们在App.vue中把需要保持活性的view使用keep-alive包裹

```vue
<!-- App.vue-->
<template>
  <div id="app">
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
    <main-tab-bar></main-tab-bar>
  </div>
</template>
```

- 做上面这些还不够，因为我们没有记录你滚动的距离，我们需要一个用一个参数来保存你当前滚动的高度，然后在浏览其他页面时切换回首页就把这个参数设置为当前位置，实现保持原来的位置。

```vue
<!-- Home.vue-->
<script>
export default {
  data() {
    return {
      saveY: 0
           };
        },
    destroyed() {//组件销毁时触发
    console.log("home destroyed");
  },
  activated() {//组件激活的时候触发 只有在keep-alive中才有
    this.$refs.scroll.scrollTo(0, this.saveY, 0);
    this.$refs.scroll.refresh();//回到Home组件时，给子组件scroll刷新一下。
  },
  deactivated() {//组件失活的时候触发 只有在keep-alive中才有
    this.saveY = this.$refs.scroll.getScrollY();
  }, ...
};
```

- 上面的deactivated方法中用到了scroll组件的getScrollY的方法。

```vue
<script>
methods: {
 getScrollY() {
   return this.scroll ? this.scroll.y : 0;
   }
}
</script>
```

- 于是我们可以随意切换组件，回到Home组件时，还是之前浏览时停留的位置。

### 详情页-导航栏的封装

- 在我们浏览首页的商品时，肯定是要点击具体的商品看其中的细节，这样的话就需要用到路由，它肯定不能在Home组件中直接展示，而是在另外一个页面组件中。
- 首页建立路由关系：打开router文件夹下的index.js

```javascript
//index.js
//导入组件
const Detail = () => import('../views/detail/Detail')
//创建router
const routes = [{
{ path: '/detail/:iid',
  component: Detail }];               
```

- 首页上的每个具体商品都是在GoodsListItem.vue组件中，我们要根据这个组件中的goodsItem的iid属性准确的点击跳转到它的专属路由页面。既然是要浏览商品的细节，当然要点击，所以要绑定一个点击事件，然后在这个事件中拿到它的goodsItemiid中的iid属性

```vue
<!-- GoodsListItem.vue-->
<template>
  <div class="goods-item" @click="itemClick">
    <img :src="goodsItem.show.img" @load="imageLoad" />
    <div class="goods-info">
      <p>{{goodsItem.title}}</p>
      <span class="price">{{goodsItem.price}}</span>
      <span class="collect">{{goodsItem.cfav}}</span>
    </div>
  </div>
</template>
<script>
    methods: {
        itemClick() {
      this.$router.push("/detail/" + this.goodsItem.iid);
    }
  }
</script>
```



- 创建Detail组件，这个组件就是商品的细节展示页面了，当然里面的细节有点多，所以我们再细分一下，给它创建导航栏 -> 子组件DetailNavBar。 这两个文件都在views文件夹下的detail文件夹，而子组件在detail文件夹下的childComps文件夹里。

```vue
<!-- DetailNavBar.vue 封装导航栏板块-->
<template>
  <div>
    <nav-bar>
      <div slot="left" class="back" @click="backClick">
        <img src="~assets/img/common/back.svg" alt />
      </div>
      <div slot="center" class="title">
        <div
          v-for="(item, index) in titles"
          :key="item.value"
          class="title-item"
          :class="{active: index === currentIndex}"
          @click="titleClick(index)"
        >{{item}}</div>
      </div>
    </nav-bar>
  </div>
</template>

<script>
    //调用了之前封装好的的NavBar
import NavBar from "components/common/navbar/NavBar";

export default {
  name: "DetailNavBar",
  components: {
    NavBar
  },
  data() {
    return {
      titles: ["商品", "参数", "评论", "推荐"],
      currentIndex: 0
    };
  },
  methods: {
    titleClick(index) {//把点击的的标题索引值赋值给currentIndex
      this.currentIndex = index;
    },
    backClick() {//点击回退上一个路由
      this.$router.back();
    }
  }
};
</script>

<style scoped>
.title {
  display: flex;
  font-size: 13px;
}
.title-item {
  flex: 1;
}
.active {
  color: var(--color-high-text);
}
.back img {
  margin-top: 12px;
}
</style>
```

- 导入封装好了的导航栏到Detail.vue

```vue
<template>
  <div id="detail">
    <detail-nav-bar></detail-nav-bar>
  </div>
</template>

<script>
import DetailNavBar from "./childComps/DetailNavBar";
export default {
  name: "Detail",
  data() {
    return {
      iid: null
    };
  },
  components: { DetailNavBar },
  created() {
    this.iid = this.$route.params.iid;
  }
};
</script>

<style scoped>
</style>
```

![1598258832194](F:\coderwhy_project\img_note\detail组件的导航栏封装效果.png)

### 详情页-数据请求以及轮播图展示

- 在前面的路由中只是获取到每个商品的iid值，现在我们想拿到商品的详细信息包括图片什么的，于是我们需要在网络层请求对应的数据传递再进行展示
- 之前Home组件的数据也是通过网络层请求过来的，所以在network文件夹下新建detail.js

```javascript
//detail.js
import {
  request
} from './request'

export function getDetail(iid) {
  return request({
    url: '/detail',
    params: {
      iid
    }
  })
}
```

- 通过iid请求到的数据通过promise获取，我们现在需要的是轮播图的数据

```vue
<!-- Detail.vue  导入DetailSwiper组件，用于展示轮播图的。top-images接收轮播图片-->
<template>
  <div id="detail">
    <detail-nav-bar></detail-nav-bar>
    <detail-swiper :top-images="topImages"></detail-swiper>
  </div>
</template>
<script>
import DetailSwiper from "./childComps/DetailSwiper";
import { getDetail } from "network/detail";
export default {
  data() {
    return {
      iid: null,
      topImages: []
    };
  },
  created() {
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      // 1.获取顶部的图片轮播数据
      console.log(res);
      this.topImages = res.result.itemInfo.topImages;
    });
  }
};
</script>
```

```vue
<!-- DetailSwiper.vue 之前封装好了的轮播图js现在也派上用场了-->
<template>
  <swiper class="detail-swiper">
    <swiper-item v-for="item in topImages" :key="item.value">
      <img :src="item" alt />
    </swiper-item>
  </swiper>
</template>

<script>
import { Swiper, SwiperItem } from "components/common/swiper";
export default {
  name: "DetailSwiper",
  components: {
    Swiper,
    SwiperItem
  },
  props: {
    topImages: {//接收并定义轮播图片
      type: Array,
      default() {
        return [];
      }
    }
  }
};
</script>

<style scoped>
.detail-swiper {
  height: 300px;
  overflow: hidden;
}
</style>
```

- 这样就可以点击商品进去展示对应的商品轮播图片了，但是我点击左上角的后退，返回到首页，点击其他的商品就会发现轮播图显示的还是之前点过的图片，这是因为我们在前面为了在页面切换时记录滚动的高度而使用了keep-alive，它会对其中的数据进行缓存，所以我们新的数据过来它并没有刷新，我们要用keep-alive中的exclude属性来指定不需要被缓存的组件。

```vue
<!-- App.vue-->
<template>
  <div id="app">
    <keep-alive exclude="Detail">
      <router-view></router-view>
    </keep-alive>
    <main-tab-bar></main-tab-bar>
  </div>
</template>
```

![1598262745928](F:\coderwhy_project\img_note\商品轮播图的封装展示.png)

### 详情页-商品基本信息的展示

- 商品的信息非常多，我们都从首页获取就太长了，所以我们在之前封装的detail.js获取商品数据里面添加获取商品详细信息。

  ```javascript
  //detail.js
  export class Goods {
    constructor(itemInfo, columns, services) {
      this.title = itemInfo.title;
      this.desc = itemInfo.desc;
      this.newPrice = itemInfo.price;
      this.oldPrice = itemInfo.oldPrice;
      this.discount = itemInfo.discountDesc;
      this.columns = columns;
      this.services = services;
      this.realPrice = itemInfo.lowNowPrice;
    }
  }
  ```

- 获取到了还不够，我们要在Detail.vue组件下接受它，然后传递给它的子组件DetailBaseInfo.vue 用于展示商品详细信息。

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">
    <detail-base-info :goods="goods"></detail-base-info>
  </div>
</template>
<script>
import DetailBaseInfo from "./childComps/DetailBaseInfo";
import { getDetail, Goods } from "network/detail";

export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {}
    };
  },
  components: { DetailNavBar, DetailSwiper, DetailBaseInfo },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      // 1.获取顶部的图片轮播数据
      console.log(res);
      const data = res.result;
      this.topImages = data.itemInfo.topImages;

      // 2.获取商品信息
      this.goods = new Goods(
        data.itemInfo,
        data.columns,
        data.shopInfo.services
      );
    });
  }
};
</script>

<style scoped>
</style>
```

```vue
<!-- DetailBaseInfo.vue-->
<template>
  <div v-if="Object.keys(goods).length !== 0" class="base-info">
    <div class="info-title">{{goods.title}}</div>
    <div class="info-price">
      <span class="n-price">{{goods.newPrice}}</span>
      <span class="o-price">{{goods.oldPrice}}</span>
      <span class="discount">{{goods.discount}}</span>
    </div>
    <div class="info-other">
      <span>{{goods.columns[0]}}</span>
      <span>{{goods.columns[1]}}</span>
      <span>{{goods.services[goods.services.length-1].name}}</span>
    </div>
    <div class="info-service">
      <span class="info-service-item" v-for="index in goods.services.length-1" :key="index.value">
        <img :src="goods.services[index-1].icon" alt />
        <span>{{goods.services[index-1].name}}</span>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "DetailBaseInfo",
  props: {
    goods: {
      type: Object,
      default() {
        return {};
      }
    }
  }
};
</script>

<style scoped>
.base-info {
  margin-top: 15px;
  padding: 0 8px;
  color: #999;
  border-bottom: 5px solid #f2f5f8;
}
.info-title {
  color: #222;
}
.info-price {
  margin-top: 10px;
}
.info-price .n-price {
  font-size: 24px;
  color: var(--color-high-text);
}
.info-price .o-price {
  font-size: 13px;
  margin-top: 5px;
  text-decoration: line-through;
}

.info-price .discount {
  font-size: 12px;
  padding: 2px 5px;
  color: #fff;
  background-color: var(--color-high-text);
  border-radius: 8px;
  margin-left: 5px;

  /*让元素上浮一些: 使用相对定位即可*/
  position: relative;
  top: -8px;
}

.info-other {
  margin-top: 15px;
  line-height: 30px;
  display: flex;
  font-size: 13px;
  border-bottom: 1px solid rgba(100, 100, 100, 0.1);
  justify-content: space-between;
}

.info-service {
  display: flex;
  justify-content: space-between;
  line-height: 60px;
}

.info-service-item img {
  width: 14px;
  height: 14px;
  position: relative;
  top: 2px;
}

.info-service-item span {
  font-size: 13px;
  color: #333;
}
</style>
```

![1598324379881](F:\coderwhy_project\img_note\商品详细信息的展示.png)

### 详情页-店铺信息的获取和展示

- 跟上面一样，首先在网络层的detail.js文件中，定义一个Shop类，获取数据

```javascript
//detail.js
export class Shop {
  constructor(shopInfo) {
    this.logo = shopInfo.shopLogo;
    this.name = shopInfo.name;
    this.fans = shopInfo.cFans;
    this.sells = shopInfo.cSells;
    this.score = shopInfo.score;
    this.goodsCount = shopInfo.cGoods;
  }
}
```

- 在Detail.vue大组件中接受Shop类数据，传递给子组件DetailShopInfo.vue

```vue
<!--Detail.vue -->
<template>
  <div id="detail">
    <detail-shop-info :shop="shop"></detail-shop-info>
  </div>
</template>
<script>
import DetailShopInfo from "./childComps/DetailShopInfo";
import { getDetail, Goods, Shop } from "network/detail";
export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {}
    };
  },
  components: { DetailNavBar, DetailSwiper, DetailBaseInfo, DetailShopInfo },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      // 1.获取顶部的图片轮播数据
      console.log(res);
      const data = res.result;
      this.topImages = data.itemInfo.topImages;

      // 2.获取商品信息
      this.goods = new Goods(
        data.itemInfo,
        data.columns,
        data.shopInfo.services
      );

      // 3.创建店铺信息的对象
      this.shop = new Shop(data.shopInfo);
    });
  }
};
</script>
```

```vue
<!-- DetailShopInfo.vue-->
<template>
  <div class="shop-info">
    <div class="shop-top">
      <img :src="shop.logo" />
      <span class="title">{{shop.name}}</span>
    </div>
    <div class="shop-middle">
      <div class="shop-middle-item shop-middle-left">
        <div class="info-sells">
          <div class="sells-count">{{shop.sells | sellCountFilter}}</div>
          <div class="sells-text">总销量</div>
        </div>
        <div class="info-goods">
          <div class="goods-count">{{shop.goodsCount}}</div>
          <div class="goods-text">全部宝贝</div>
        </div>
      </div>
      <div class="shop-middle-item shop-middle-right">
        <table>
          <tr v-for="(item, index) in shop.score" :key="index">
            <td>{{item.name}}</td>
            <td class="score" :class="{'score-better': item.isBetter}">{{item.score}}</td>
            <td class="better" :class="{'better-more': item.isBetter}">
              <span>{{item.isBetter ? '高':'低'}}</span>
            </td>
          </tr>
        </table>
      </div>
    </div>
    <div class="shop-bottom">
      <div class="enter-shop">进店逛逛</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DetailShopInfo",
  props: {
    shop: {
      type: Object
    }
  },
  filters: {
    sellCountFilter: function(value) {
      if (value < 10000) return value;
      return (value / 10000).toFixed(1) + "万";
    }
  }
};
</script>

<style scoped>
.shop-info {
  padding: 25px 8px;
  border-bottom: 5px solid #f2f5f8;
}

.shop-top {
  line-height: 45px;
  /* 让元素垂直中心对齐 */
  display: flex;
  align-items: center;
}

.shop-top img {
  width: 45px;
  height: 45px;
  border-radius: 50%;
  border: 1px solid rgba(0, 0, 0, 0.1);
}

.shop-top .title {
  margin-left: 10px;
  vertical-align: center;
}

.shop-middle {
  margin-top: 15px;
  display: flex;
  align-items: center;
}

.shop-middle-item {
  flex: 1;
}

.shop-middle-left {
  display: flex;
  justify-content: space-evenly;
  color: #333;
  text-align: center;
  border-right: 1px solid rgba(0, 0, 0, 0.1);
}

.sells-count,
.goods-count {
  font-size: 18px;
}

.sells-text,
.goods-text {
  margin-top: 10px;
  font-size: 12px;
}

.shop-middle-right {
  font-size: 13px;
  color: #333;
}

.shop-middle-right table {
  width: 120px;
  margin-left: 30px;
}

.shop-middle-right table td {
  padding: 5px 0;
}

.shop-middle-right .score {
  color: #5ea732;
}

.shop-middle-right .score-better {
  color: #f13e3a;
}

.shop-middle-right .better span {
  background-color: #5ea732;
  color: #fff;
  text-align: center;
}

.shop-middle-right .better-more span {
  background-color: #f13e3a;
}

.shop-bottom {
  text-align: center;
  margin-top: 10px;
}

.enter-shop {
  display: inline-block;
  font-size: 14px;
  background-color: #f2f5f8;
  width: 150px;
  height: 30px;
  text-align: center;
  line-height: 30px;
  border-radius: 10px;
}
</style>
```

![1598325537827](F:\coderwhy_project\img_note\商品店铺信息的展示.png)

### 详情页-加入滚动的效果Scroll

- 之前封装的Scroll 可以让页面进行滚动。现在我们需要在商品的详细页面中加入这个效果。

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav"></detail-nav-bar>
    <scroll class="content">
      <detail-swiper :top-images="topImages"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
    </scroll>
  </div>
</template>

<script>
import DetailNavBar from "./childComps/DetailNavBar";
import DetailSwiper from "./childComps/DetailSwiper";
import DetailBaseInfo from "./childComps/DetailBaseInfo";
import DetailShopInfo from "./childComps/DetailShopInfo";

import Scroll from "components/common/scroll/Scroll";

import { getDetail, Goods, Shop } from "network/detail";

export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {}
    };
  },
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    Scroll
  },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      // 1.获取顶部的图片轮播数据
      console.log(res);
      const data = res.result;
      this.topImages = data.itemInfo.topImages;

      // 2.获取商品信息
      this.goods = new Goods(
        data.itemInfo,
        data.columns,
        data.shopInfo.services
      );

      // 3.创建店铺信息的对象
      this.shop = new Shop(data.shopInfo);
    });
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
  height: calc(100% - 44px);
}
</style>
```

![1598326176302](F:\coderwhy_project\img_note\商品详细页面加入滚动效果.png)

### 详情页-商品详细数据展示

- 店铺信息的下边，是商品具体的数据和模特展示的细节图，我们一样也要拿到，不过这次需要的数据不是很多，所以我们没有必要在detail.js里边新建一个类来定义获取，直接在Detail.vue获取

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">
    <scroll class="content" ref="scroll">
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"></detail-goods-info>
    </scroll>
  </div>
</template>

<script>
import DetailGoodsInfo from "./childComps/DetailGoodsInfo";

import Scroll from "components/common/scroll/Scroll";


export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      detailInfo: {}
    };
  },
  components: {
    DetailGoodsInfo
  },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
        const data = res.result;
      // 4.保存商品的详细数据
      this.detailInfo = data.detailInfo;
    });
  },
  methods: {
    imageLoad() {//由于图片过来的速度会影响scroll的高度，所以我们在图片加载完成之后刷新页面，重置滚动的高度。
      this.$refs.scroll.refresh();
    }
  }
};
</script>
```

```vue
<!-- DetailGoodsInfo.vue-->
<template>
  <div v-if="Object.keys(detailInfo).length !== 0" class="goods-info">
    <div class="info-desc clear-fix">
      <div class="start"></div>
      <div class="desc">{{detailInfo.desc}}</div>
      <div class="end"></div>
    </div>
    <div class="info-key">{{detailInfo.detailImage[0].key}}</div>
    <div class="info-list">
      <img
        v-for="(item, index) in detailInfo.detailImage[0].list"
        :src="item"
        :key="index.value"
        @load="imgLoad"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "DetailGoodsInfo",
  props: {
    detailInfo: {
      type: Object
    }
  },
  data() {
    return {
      counter: 0,
      imagesLength: 0
    };
  },
  methods: {
    imgLoad() {
      //判断，所有的图片都加载完了，那么进行一次回调就可以了
      if (++this.counter === this.imagesLength) {
        this.$emit("imageLoad");
      }
    }
  },
  watch: {
    //用来监听某个属性的变化，并调用因为变化需要执行的方法
    detailInfo() {
      //获取图标的个数
      this.imagesLength = this.detailInfo.detailImage[0].list.length;
    }
  }
};
</script>

<style scoped>
.goods-info {
  padding: 20px 0;
  border-bottom: 5px solid #f2f5f8;
}

.info-desc {
  padding: 0 15px;
}

.info-desc .start,
.info-desc .end {
  width: 90px;
  height: 1px;
  background-color: #a3a3a5;
  position: relative;
}

.info-desc .start {
  float: left;
}

.info-desc .end {
  float: right;
}

.info-desc .start::before,
.info-desc .end::after {
  content: "";
  position: absolute;
  width: 5px;
  height: 5px;
  background-color: #333;
  bottom: 0;
}

.info-desc .end::after {
  right: 0;
}

.info-desc .desc {
  padding: 15px 0;
  font-size: 14px;
}

.info-key {
  margin: 10px 0 10px 15px;
  color: #333;
  font-size: 15px;
}

.info-list img {
  width: 100%;
}
</style>
```

![1598327907679](F:\coderwhy_project\img_note\商品详细数据信息被展示.png)

### 商品参数信息的展示

- 在商品详情数据的下面还有商品参数的信息，这次需要的数据较多，需要在网络层detail.js文件中建立类来获取。

  ```javascript
  //detail.js
  export class GoodsParam {
    constructor(info, rule) {
      // 注： images可能没有值(某些商品有值，某些没有值)
      this.image = info.images ? info.images[0] : "";
      this.infos = info.set;
      this.sizes = rule.tables;
    }
  }
  ```

- 在Detail.vue大组件中接收参数信息并传递给子组件DetailParamInfo.vue用于展示

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">
    <scroll class="content" ref="scroll">
      <detail-param-info :param-info="paramInfo"></detail-param-info>
    </scroll>
  </div>
</template>
<script>
import DetailParamInfo from "./childComps/DetailParamInfo";
import Scroll from "components/common/scroll/Scroll";
import { getDetail, Goods, Shop, GoodsParam } from "network/detail";
export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      paramInfo: {}
    };
  },
  components: {
    DetailParamInfo
  },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      // 1.获取顶部的图片轮播数据
      const data = res.result;
      // 5.获取参数的信息
      this.paramInfo = new GoodsParam(
        data.itemParams.info,
        data.itemParams.rule
      );
    });
  },
};
</script>    
```

```vue
<!-- DetailParamInfo.vue-->
<template>
  <div class="param-info" v-if="Object.keys(paramInfo).length !== 0">
    <table v-for="(table, index) in paramInfo.sizes" class="info-size" :key="index">
      <tr v-for="(tr, indey) in table" :key="indey">
        <td v-for="(td, indez) in tr" :key="indez">{{td}}</td>
      </tr>
    </table>
    <table class="info-param">
      <tr v-for="(info, index) in paramInfo.infos" :key="index.value">
        <td class="info-param-key">{{info.key}}</td>
        <td class="param-value">{{info.value}}</td>
      </tr>
    </table>
    <div class="info-img" v-if="paramInfo.image.length !== 0">
      <img :src="paramInfo.image" alt />
    </div>
  </div>
</template>

<script>
export default {
  name: "DetailParamInfo",
  props: {
    paramInfo: {
      type: Object,
      default() {
        return {};
      }
    }
  }
};
</script>

<style scoped>
.param-info {
  padding: 20px 15px;
  font-size: 14px;
  border-bottom: 5px solid #f2f5f8;
}

.param-info table {
  width: 100%;
  border-collapse: collapse;
}

.param-info table tr {
  height: 42px;
}

.param-info table tr td {
  border-bottom: 1px solid rgba(100, 100, 100, 0.1);
}

.info-param-key {
  /*当value的数据量比较大的时候, 会挤到key,所以给一个固定的宽度*/
  width: 95px;
}

.info-param {
  border-top: 1px solid rgba(0, 0, 0, 0.1);
}

.param-value {
  color: #eb4868;
}

.info-img img {
  width: 100%;
}
</style>
```

![1598329035777](F:\coderwhy_project\img_note\商品参数信息的展示.png)

### 详情页-商品评论信息的展示

- 在商品参数信息的后面就是对该商品的评价了，都是由买过的买家发布的，也是一样，要去拿数据，不过有些商品有评价，有些商品没有评价，所以要做判断

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">    
    <scroll class="content" ref="scroll">
      <detail-comment-info :comment-info="commentInfo"></detail-comment-info>
    </scroll>
  </div>
</template>

<script>
import DetailCommentInfo from "./childComps/DetailCommentInfo";

import Scroll from "components/common/scroll/Scroll";

import { getDetail, Goods, Shop, GoodsParam } from "network/detail";

export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      commentInfo: {}
    };
  },
  components: {
    DetailCommentInfo
  },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;
    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      console.log(res);
      // 1.获取数据
      const data = res.result;
      // 7.取出评论的信息
      if (data.rate.cRate !== 0) {
        this.commentInfo = data.rate.list[0];
      }
    });
  }
};
</script>
```

- 在评价的组件DetailCommentInfo.vue中用到了时间戳，这是买家发布评价记录的一个数据，我们需要把它解析成对应的年月日，在JavaScript中有Date类，可以解析时间，但是通过这个一步步获取要写很多代码，所以我们用网上别人已经封装好了的。 因为这个是工具，所以我们把这个封装的代码放到common文件夹下的utils.js中

```javascript
export function formatDate(date, fmt) {
  if (/(y+)/.test(fmt)) {
    fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
  }
  let o = {
    'M+': date.getMonth() + 1,
    'd+': date.getDate(),
    'h+': date.getHours(),
    'm+': date.getMinutes(),
    's+': date.getSeconds()
  };
  for (let k in o) {
    if (new RegExp(`(${k})`).test(fmt)) {
      let str = o[k] + '';
      fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? str : padLeftZero(str));
    }
  }
  return fmt;
};

function padLeftZero(str) {
  return ('00' + str).substr(str.length);
};

```

- 在子组件中编写评价数据的展示信息，导入封装好的解析时间的js代码。

```vue
<!-- DetailCommentInfo.vue-->
<template>
  <div>
    <div v-if="Object.keys(commentInfo).length !== 0" class="comment-info">
      <div class="info-header">
        <div class="header-title">用户评价</div>
        <div class="header-more">
          更多
          <i class="arrow-right"></i>
        </div>
      </div>
      <div class="info-user">
        <img :src="commentInfo.user.avatar" alt />
        <span>{{commentInfo.user.uname}}</span>
      </div>
      <div class="info-detail">
        <p>{{commentInfo.content}}</p>
        <div class="info-other">
          <span class="date">{{commentInfo.created | showDate}}</span>
          <span>{{commentInfo.style}}</span>
        </div>
        <div class="info-imgs">
          <img :src="item" v-for="(item, index) in commentInfo.images" :key="index.value" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { formatDate } from "common/utils";
export default {
  name: "DetailCommentInfo",
  props: {
    commentInfo: {
      type: Object,
      default() {
        return {};
      }
    }
  },
  filters: {
    showDate: function(value) {
      let date = new Date(value * 1000);
      return formatDate(date, "yyyy-MM-dd");
    }
  }
};
</script>

<style scoped>
.comment-info {
  padding: 5px 12px;
  color: #333;
  border-bottom: 5px solid #f2f5f8;
}

.info-header {
  height: 50px;
  line-height: 50px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.header-title {
  float: left;
  font-size: 15px;
}

.header-more {
  float: right;
  margin-right: 10px;
  font-size: 13px;
}

.info-user {
  padding: 10px 0 5px;
}

.info-user img {
  width: 42px;
  height: 42px;
  border-radius: 50%;
}

.info-user span {
  position: relative;
  font-size: 15px;
  top: -15px;
  margin-left: 10px;
}

.info-detail {
  padding: 0 5px 15px;
}

.info-detail p {
  font-size: 14px;
  color: #777;
  line-height: 1.5;
}

.info-detail .info-other {
  font-size: 12px;
  color: #999;
  margin-top: 10px;
}

.info-other .date {
  margin-right: 8px;
}

.info-imgs {
  margin-top: 10px;
}

.info-imgs img {
  width: 70px;
  height: 70px;
  margin-right: 5px;
}
</style>
```

![1598410190709](F:\coderwhy_project\img_note\商品评价信息的展示.png)

### 商品推荐数据的展示

- 由于商品推荐数据在另外一个接口上，所以我们要修改请求网络层的url。

```javascript
//detail.js
export function getRecommend() {
  return request({
    url: '/recommend'
  })
}
```

- 把这个方法导入到Detail.vue大组件中。由于推荐数据的布局和数据格式都和首页商品类似，所以我们可以直接用之前封装好的GoodsList.vue组件。

```vue
<template>
  <div id="detail">
    <scroll class="content" ref="scroll">
      <goods-list :goods="recommends"></goods-list>
    </scroll>
  </div>
</template>

<script>
import Scroll from "components/common/scroll/Scroll";

import GoodsList from "components/content/goods/GoodsList";

import {
  getRecommend
} from "network/detail";

export default {
  name: "Detail",
  data() {
    return {
      iid: null,
      recommends: []
    };
  },
  components: {
    GoodsList
  },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid;

    // 3.请求推荐数据
    getRecommend().then(res => {
      this.recommends = res.data.list;
    });
  },
  
};
</script>
```

- 在网页上显示时却发现img not undefined 仔细一看接口的数据，推荐商品的接口img直接在list里面，首页中的商品img在list下面的show里面，所以会找不到。由于展示的图在GoodsList.vue下面的子组件GoodsListItem.vue中，所以我们直接在这个子组件中添加个判断就好了。

```vue
<!-- GoodsListItem.vue-->
<template>
  <div class="goods-item" @click="itemClick">
    <img :src="showImage" @load="imageLoad" />
  </div>
</template>

<script>
export default {
  name: "GoodsListItem",
  props: {
    goodsItem: {
      type: Object,
      default() {
        return [];
      }
    }
  },
  computed: {
    showImage() {//前面一个找不到图片就执行后面一个
      return this.goodsItem.image || this.goodsItem.show.img;
    }
  }
};
</script>
```

![1598424003722](F:\coderwhy_project\img_note\推荐商品的展示.png)

### 混入mixin的使用

- 在我们的推荐商品中用到了首页的商品，他们都需要加载图片，也需要监听页面的高度，用到了better-scroll插件，需要对页面实时的刷新，来重置页面的高度，所以Home.vue组件和Detail.vue组件中的mounted属性代码是一样。可以抽离封装起来。

- 在common文件夹下新建mixin.js，放入公共属性和方法，然后在需要这些属性和方法的组件中直接使用。

```javascript
//mixin.js
import {
  debounce
} from './utils'

export const itemListenerMixin = {
  data() {
    return {
      itemImgListener: null,
      newRefresh: null
    }
  },
  mounted() {
    this.newRefresh = debounce(this.$refs.scroll.refresh, 100)

    this.itemImgListener = () => {
      this.newRefresh()
    }
    this.$bus.$on('itemImageLoad', this.itemImgListener)
    // console.log('我是混入的内容')
  }
}
```

- 在Home组件中使用mixin

```vue
<script>
import { itemListenerMixin } from "common/mixin";
 export default {
   mixins: [itemListenerMixin], 
     deactivated() {
    // 2.取消全局事件的监听
    this.$bus.$off("itemImgLoad", this.itemImgListener);
  },
   }
```

- 在Detail组件中使用mixin

```vue
<script>
import { itemListenerMixin } from "common/mixin";
export default {
mixins: [itemListenerMixin],    
    destroyed() {
    this.$bus.$off("itemImgLoad", this.itemImgListener);
  },
}
```

### 点击标题，滚动到对应的主题

- 在详情页里的顶部，之前做了点击高亮的效果，目前不仅仅要这个效果，还需要点击之后页面滚动到对应的区域，提升用户体验。
- 需要动态的获取他们主题的高度，那么我们需要知道他们的组件区域的DOM元素，这样需要用到ref获取。然后在data属性里面创建themeTopYs属性，用于存放DOM元素的距离顶部的高度。
- 直接在mounted属性中获取和created中获取是获取不到的。因为图片加载需要时间，虽然DOM元素加载出来了，但是图片加载慢的话就会使点击滚动的高度达不到预期的效果，所以我们使用getThemeTopY属性通过防抖来执行获取数据。

```vue
<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick"></detail-nav-bar>
    <scroll class="content" ref="scroll">
      <detail-swiper :top-images="topImages"></detail-swiper>
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"></detail-goods-info>
      <detail-param-info ref="params" :param-info="paramInfo"></detail-param-info>
      <detail-comment-info ref="comment" :comment-info="commentInfo"></detail-comment-info>
      <goods-list ref="recommend" :goods="recommends"></goods-list>
    </scroll>
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

import Scroll from "components/common/scroll/Scroll";
import GoodsList from "components/content/goods/GoodsList";

import { debounce } from "common/utils";
import { itemListenerMixin } from "common/mixin";

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
      themeTopYs: [],
      getThemeTopY: null
    };
  },
  mixins: [itemListenerMixin],
  created() {
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
    // 4.给getThemeTopY赋值(对给this.themeTopYs赋值的操作进行防抖)
    this.getThemeTopY = debounce(() => {
      console.log("----");
      this.themeTopYs = [];
      this.themeTopYs.push(0);
      this.themeTopYs.push(this.$refs.params.$el.offsetTop);
      this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
      this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);

      console.log(this.themeTopYs);
    }, 200);
  },
  destroyed() {
    this.$bus.$off("itemImgLoad", this.itemImgListener);
  },
  methods: {
    imageLoad() {
      console.log("---");
      this.newRefresh();
      // this.$refs.scroll.refresh();
      this.getThemeTopY();
    },
    titleClick(index) {
      this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 200);
      // this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 200);
      console.log(index);
    }
  },
  mounted() {}
};
</script>
```

![1598452363681](F:\coderwhy_project\img_note\点击标题滚动到对应区域.png)

### 做项目中遇到的坑

- 每一个组件都要加样式作用域 也就是在style标签中：`<style scoped> ...</style>`，不然的话在其他组件中如果创建了两个名字相同的样式文件，那么有可能覆盖，以至于达不到你原本做的样式，在这里遇到过一次，然后找了一下午的bug。在一个组件中无缘无故多了个position：absolute，以至于打开页面看到的效果是自动向下滚动到该组件上，还以为是哪里引用了滚动方法。以后一定要多加注意！

### 滚动详情页内容显示对应标题

- 上面我们做到了点击顶部的标题滚动到对应的区域，但是现在我们滚动到标题对应的区域它没有自动高亮标题。
- 这里我们需要获取到滚动的高度，根据高度和上面themeTopYs里面获取到的高度进行比对，符合条件的就把索引赋值给DetailNavBar组件里面保存的标题数组，就实现了滚动内容，高亮显示对应的标题。

```vue
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
  </div>
</template>
<script>
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
      currentIndex: 0
    };
  },
  methods: { 
      contentScroll(position) {
      // 1.获取y值
      const positionY = -position.y;
      // 2.positionY和主题中的值进行对比
      // [0, 735, 2157, 2352]
      // positionY 在 0 ~ 735 ， index = 0
      // positionY 在 =735 ~ 2157 ， index = 1
      // positionY 在 =2157 ~ 2352 ， index = 2
      // positionY 大于等于2352 ， index = 3

      let length = this.themeTopYs.length;
      for (let i = 0; i < length; i++) {
        if (
          this.currentIndex !== i &&
          ((i < length - 1 &&
            positionY >= this.themeTopYs[i] &&
            positionY < this.themeTopYs[i + 1]) ||
            (i === length - 1 && positionY >= this.themeTopYs[i]))
        ) {
          this.currentIndex = i;
          this.$refs.nav.currentIndex = this.currentIndex;
        }
      }
    }
  }
};
</script>
```

![1598541821751](F:\coderwhy_project\img_note\滚动内容高亮对应的标题.png)

### 对复杂判断条件的分析与优化

- 在上面的滚动内容高亮显示对应的标题我们做了很长的判断条件。有没有更好的方法呢？
- 我们在原先的基础上多加了个最大值， 使得themeTopY数组长度为5，那么在判断高度大于推荐商品内容高度的时候就可以和前面的判断条件变为一致：推荐内容高度~最大值，数组长度变长了也不会出现i+1=undefined的了。

```vue
<script>
export default {
  created() {
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
   },
  methods: {
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
          positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i + 1]
        ) {
          this.currentIndex = i;
          this.$refs.nav.currentIndex = this.currentIndex;
        }
      }
    }
  }
};
</script>
```

- 这样我们的判断条件会简洁很多，而且性能会提升一些，效果也和之前一样。

### 详情页-底部工具栏的封装

- 底部的组件不需要滚动，它固定在最下面，所以我们把该组件放在scroll组件外的下面。

```vue
<!-- DetailBottomBar.vue-->
<template>
  <div class="bottom-bar">
    <div class="bar-item bar-left">
      <div>
        <i class="icon service"></i>
        <span class="text">客服</span>
      </div>
      <div>
        <i class="icon shop"></i>
        <span class="text">店铺</span>
      </div>
      <div>
        <i class="icon select"></i>
        <span class="text">收藏</span>
      </div>
    </div>
    <div class="bar-item bar-right">
      <div class="cart" @click="addToCart">加入购物车</div>
      <div class="buy">购买</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DetailBottomBar",
  methods: {
    addToCart() {
      this.$emit("addToCart");
    }
  }
};
</script>

<style scoped>
.bottom-bar {
  height: 58px;
  position: fixed;
  background-color: #fff;
  left: 0;
  right: 0;
  bottom: 0;

  display: flex;
  text-align: center;
}

.bar-item {
  flex: 1;
  display: flex;
}

.bar-item > div {
  flex: 1;
}

.bar-left .text {
  font-size: 13px;
}

.bar-left .icon {
  display: block;
  width: 22px;
  height: 22px;
  margin: 10px auto 3px;
  background: url("~assets/img/detail/detail_bottom.png") 0 0/100%;
}

.bar-left .service {
  background-position: 0 -54px;
}

.bar-left .shop {
  background-position: 0 -98px;
}

.bar-right {
  font-size: 15px;
  color: #fff;
  line-height: 58px;
}

.bar-right .cart {
  background-color: #ffe817;
  color: #333;
}

.bar-right .buy {
  background-color: #f69;
}
</style>
```

```vue
<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"></detail-nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      ...
    </scroll>
    <detail-bottom-bar></detail-bottom-bar>
  </div>
</template>

<script>
import DetailBottomBar from "./childComps/DetailBottomBar";
export default {
  components: {
    DetailBottomBar,
  },
  }
};
</script>

<style scoped>
.content {
  position: relative;
  height: calc(100% - 99px);
  overflow: hidden;
}
</style>
```

- 由于底部固定的组件遮挡了滚动的最后两个推荐商品的一部分图，所以我们要把滚动的区域再往上提高一些，高度为这个组件的高度。所以整体的高度 -99px

![1598580765363](F:\coderwhy_project\img_note\详情页底部工具栏的封装启用的效果.png)

### 详情页-BackTop组件的混入封装

- 在之前的首页，我们用到了BackTop组件，它的作用是当页面滚动到一定高度时，显示该组件，然后点击可以使页面滚动到顶部，该组件就隐藏。现在我们需要在Detail组件中也使用这个组件，由于他们的功能是相同的，没必要再写一遍。于是我们可以把该组件抽离出来，放到mixin.js文件中，然后再在需要的组件中使用即可。

```javascript
//mixin.js
export const backTopMixin = {
  components: {
    BackTop
  },
  data() {
    return {
      isShowBackTop: false
    }
  },
  methods: {
    backTop() {
      this.$refs.scroll.scrollTo(0, 0, 300)
    },
    listenShowBackTop(position) {
      this.isShowBackTop = -position.y > 1000
    }
  }
}
```

- 上面抽离的内容全部都是在Home组件中抽出来的。我们先在Detail组件中使用看看效果如何

```vue
<template>
  <div id="detail">
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      ...
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>
    <detail-bottom-bar></detail-bottom-bar>
  </div>
</template>
<script>
import { itemListenerMixin, backTopMixin } from "common/mixin";
export default {
  mixins: [itemListenerMixin, backTopMixin],  
  methods: {
    backClick() {
      this.backTop();
    },  
      contentScroll(position) {
        this.listenShowBackTop(position);
    }
  }
};
</script>
```

- Home组件里面也是同理，把之前写的注释掉导入mixin即可。

![1598585439699](F:\coderwhy_project\img_note\详情页中tabTop组件的混入封装.png)

### 将商品添加到购物车

- 我们在浏览商品之后可以在下面点击加入购物车，实现记录的一个效果，购物车整个数据包括商品的数量、价格、标题、描述，所以我们要用数组存放起来，而且购物车的数据不仅仅是要在一个页面展示的，他可能要携带去另外一个组件中去进行结算更改或者删除，所以我们需要用到Vuex来管理数据。通过npm install vuex@3.1.0 --save 进行安装。
- 安装之后就要去对应的仓管管理文件夹里创建新的文件了，也就是在项目下的store的文件夹新建index.js，然后我们需要安装Vuex到vue对象上。

```javascript
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

// 1.安装插件
Vue.use(Vuex)

// 2.创建Stroe对象
const store = new Vuex.Store({
  state: {
 
  },
  mutations: {
  }
})

// 3.挂载到Vue实例上
export default store
```

- 在整个项目的入口文件 main.js中挂载Vuex

```javascript
//main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

Vue.config.productionTip = false

Vue.prototype.$bus = new Vue()

new Vue({
  render: h => h(App),
  router,
  store
}).$mount('#app')
```

- 安装挂载好之后我们可以来使用Vuex管理数据了。

```vue
<!-- DetailBottomBar.vue 触发点击事件 把addToCart事件传递给Detail组件-->
<template>
  <div class="bottom-bar">
    <div class="bar-item bar-left">
      <div>
        <i class="icon service"></i>
        <span class="text">客服</span>
      </div>
      <div>
        <i class="icon shop"></i>
        <span class="text">店铺</span>
      </div>
      <div>
        <i class="icon select"></i>
        <span class="text">收藏</span>
      </div>
    </div>
    <div class="bar-item bar-right">
      <div class="cart" @click="addToCart">加入购物车</div>
      <div class="buy">购买</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DetailBottomBar",
  methods: {
    addToCart() {
      this.$emit("addToCart");
    }
  }
};
</script>
```

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">
    <detail-bottom-bar @addToCart="addToCart"></detail-bottom-bar>
  </div>
</template>

<script>
export default {    
  methods: {
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
      //this.$store.commit("addCart", product);
        this.$store.dispatch("addCart", product);
    }
  }
};
</script>
```

### Vuex代码的重构

- 在store文件夹中，我们要把Vuex对象下的每个属性分别做一个文件，然后倒入使用，使得代码的逻辑清晰。 在store文件夹中新建actions.js文件和mutations.js文件夹和mutation-types.js文件

```javascript
//mutation-types.js 存放方法的默认名
export const ADD_COUNTER = 'add_counter'
export const ADD_TO_CART = 'add_to_cart'
```

```javascript
//actions.js
import {
  ADD_COUNTER,
  ADD_TO_CART
} from './mutation-types'
export default {
  addCart(context, payload) {
    //   1.查找之前数组中是否有该商品
    let oldProduct = context.state.cartList.find(item => item.iid === payload.iid)
    //   2.判断oldProduct
    if (oldProduct) {
      //   oldProduct.count += 1
      context.commit(ADD_COUNTER, oldProduct)
    } else {
      payload.count = 1
      //   context.state.cartList.push(payload)
      context.commit(ADD_TO_CART, payload)
    }
  }
}
```

```javascript
//mutations.js
import {
  ADD_COUNTER,
  ADD_TO_CART
} from './mutation-types'

export default {
  // mutations 唯一的目的就是修改state中的状态
  //    mutations中的每个方法尽可能完成的事件比较单一一点
  [ADD_COUNTER](state, payload) {
    payload.count++
  },
  [ADD_TO_CART](state, payload) {
    state.cartList.push(payload)
  }
}
```

- 于是index.js文件就很简洁了

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

import mutations from './mutations'
import actions from './actions'

// 1.安装插件
Vue.use(Vuex)

// 2.创建Stroe对象
const state = {
  cartList: []
}
const store = new Vuex.Store({
  state,
  mutations,
  actions

})

// 3.挂载到Vue实例上
export default store
```

### 购物车-导航栏实现

- 在做Cart组件的导航栏之前我们需要在store文件夹中新建getters.js文件，和之前一样，为了简化index.js中的复杂度，我们把计算属性抽离出来
- 这里做的是点击加入购物车商品的数量，也就是保存它的数组长度。

```javascript
//getters.js
export default {
  cartLength(state) {
    return state.cartList.length;
  },
  cartList(state) {
    return state.cartList()
  }
}
```

- 在index.js文件中引入getters.js

```javascript
//index.js
import Vue from 'vue'
import Vuex from 'vuex'

import mutations from './mutations'
import actions from './actions'
import getters from './getters'

// 1.安装插件
Vue.use(Vuex)

// 2.创建Stroe对象
const state = {
  cartList: []
}
const store = new Vuex.Store({
  state,
  mutations,
  actions,
  getters
})

// 3.挂载到Vue实例上
export default store
```

- 接下来就可以在Cart.vue 也就是购物车组件中布局放置数据了。

```vue
<!-- Cart.vue-->
<template>
  <div>
    <nav-bar class="nav-bar">
      <div slot="center">购物车({{ length }})</div>
    </nav-bar>
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";

import { mapGetters } from "vuex"; //这个方法是vuex自带的，可以辅助getters属性的简化

export default {
  name: "Cart",
  components: {
    NavBar
  },
  computed: {
    // 两种语法
    // ...mapGetters(['cartLength', 'cartList'])
    ...mapGetters({
      //前面的是使用的别名，后面的是getters里面的计算方法
      length: "cartLength", 
      list: "cartList"
    })
  }
};
</script>

<style scoped>
.nav-bar {
  background-color: var(--color-tint);
  color: #fff;
}
</style>
```

- 当我们在首页中选购商品，点击加入购物车之后返回到首页，点击下方购物车按钮，进来的效果

![1598605316324](F:\coderwhy_project\img_note\购物车-导航栏的实现效果.png)

### 购物车-购物车商品列表展示

- 现在可以把Detail.vue中获取的数据通过Vuex传递给Cart.vue中，那么我们现在需要把详细的数据展示到Cart组件里，于是我们创建一个CartList组件，再创建一个CartListItem组件用于展示加入购物车的每一个商品。这两个组件都放在Cart组件的childComps文件夹里。

```vue
<!-- Cart.vue-->
<template>
  <div>
    <cart-list></cart-list>
  </div>
</template>

<script>
import CartList from "./childComps/CartList";
export default {
  name: "Cart",
  components: {
    NavBar,
    CartList
  },
};
</script>
```

```vue
<!-- CartList.vue-->
<template>
  <div class="cart-list">
    <scroll class="content">
      <cart-list-item v-for="(item, index) in cartList" :key="index.value" :product="item"></cart-list-item>
    </scroll>
  </div>
</template>

<script>
import Scroll from "components/common/scroll/Scroll";
import CartListItem from "./CartListItem";

import { mapGetters } from "vuex";

export default {
  name: "CartList",
  components: {
    Scroll,
    CartListItem
  },
  computed: {
    ...mapGetters(["cartList"])
  }
};
</script>

<style scoped>
.cart-list {
  height: calc(100vh - 44px);
}
.content {
  height: 100%;
}
</style>
```

```vue
<!-- CartListItem.vue-->
<template>
  <div>{{product}}</div>
</template>

<script>
export default {
  name: "CartListItem",
  props: {
    product: {
      type: Object,
      default() {
        return {};
      }
    }
  }
};
</script>

<style scoped>
</style>
```

- 在这上面用到了Scroll组件，使页面的滚动用户体验更好。然后打开网站，在首页中加入了8个商品到购物车，下面是Cart组件展示的效果。

  ![1598661559469](F:\coderwhy_project\img_note\购物车-商品列表的展示效果.png)

### 购物车-购物车列表模块的优化和商品选中按钮的创建

- 上面可以看见，我们把数据能够展示到Cart组件里了，我们现在需要把每一个数据都有序而且好看的排列出来。于是我们对CartListItem组件进行优化。到购物车的商品默认都是要购买的，于是我们在每个商品的前面加上一个按钮并且是选中状态，而用户可以在这之上对每个商品可以点击取消选中。

```vue
<!-- CartListItem.vue -->
<template>
  <div id="shop-item">
    <div class="item-selector">
      <check-button :is-checked="itemInfo.checked" @click.native="checkClick"></check-button>
    </div>
    <div class="item-img">
      <img :src="itemInfo.image" alt="商品图片" />
    </div>
    <div class="item-info">
      <div class="item-title">{{itemInfo.title}}</div>
      <div class="item-desc">商品描述: {{itemInfo.desc}}</div>
      <div class="info-bottom">
        <div class="item-price left">¥{{itemInfo.price}}</div>
        <div class="item-count right">x{{itemInfo.count}}</div>
      </div>
    </div>
  </div>
</template>

<script>
import CheckButton from "components/content/checkButton/CheckButton";

export default {
  name: "CartListItem",
  components: {
    CheckButton
  },
  props: {
    itemInfo: {
      type: Object,
      default() {
        return {};
      }
    }
  },
  methods: {
    checkClick() {
      this.itemInfo.checked = !this.itemInfo.checked;
    }
  }
};
</script>

<style scoped>
#shop-item {
  width: 100%;
  display: flex;
  font-size: 0;
  padding: 5px;
  border-bottom: 1px solid #ccc;
}

.item-selector {
  width: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.item-title,
.item-desc {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.item-img {
  padding: 5px;
  /*border: 1px solid #ccc;*/
}

.item-img img {
  width: 80px;
  height: 100px;
  display: block;
  border-radius: 5px;
}

.item-info {
  font-size: 17px;
  color: #333;
  padding: 5px 10px;
  position: relative;
  overflow: hidden;
}

.item-info .item-desc {
  font-size: 14px;
  color: #666;
  margin-top: 15px;
}

.info-bottom {
  margin-top: 10px;
  position: absolute;
  bottom: 10px;
  left: 10px;
  right: 10px;
}

.info-bottom .item-price {
  color: orangered;
}
</style>
```

- 选中按钮的模块我们用CheckButton组件创建，并且该组件在components文件夹下的content文件夹下的checkButton文件夹下。

```vue
<!-- CheckButton.vue-->
<template>
  <div class="check-button" :class="{check: isChecked}">
    <img src="~assets/img/cart/tick.svg" alt />
  </div>
</template>

<script>
export default {
  name: "CheckButton",
  props: {
    isChecked: {
      type: Boolean,
      default: true
    }
  }
};
</script>

<style scoped>
.check-button {
  border-radius: 50%;
  border: 2px solid #aaa;
}
.check {
  border-color: red;
  background-color: red;
}
</style>
```

- 因为每个商品的选中数据是由Vuex来管理并且加上的，我们要在mutations.js文件中进行默认设置。

```javascript
//mutations.js
import {
  ADD_COUNTER,
  ADD_TO_CART
} from './mutation-types'

export default {
  // mutations 唯一的目的就是修改state中的状态
  //    mutations中的每个方法尽可能完成的事件比较单一一点
  [ADD_COUNTER](state, payload) {
    payload.count++
  },
  [ADD_TO_CART](state, payload) {
    payload.checked = true //给加入购物车的商品加入默认属性checked 并且为选中状态
    state.cartList.push(payload)
  }
}
```

![1598686651518](F:\coderwhy_project\img_note\购物车列表的展示和选中按钮的展示.png)

### 购物车-底部工具的封装和应用

- 现在我们的商品都可以选择了，但是我们现在需要去结算，并且可以在选中商品的时候，自动计算出这些选中商品的总价格。于是我们在cart文件夹下的childComps文件夹下创建CartBottomBar组件

```vue
<!-- CartBottomBar.vue-->
<template>
  <div class="bottom-bar">
    <div class="check-content">
      <check-button class="check-button"></check-button>
      <span>全选</span>
    </div>
    <div class="price">合计： {{totalPrice}}</div>
    <div class="calculate">去计算： ({{checkLength}})</div>
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
```

- 这个组件要放置在Cart组件的商品列表下面

```vue
<!-- Cart.vue-->
<template>
  <div>
    <nav-bar class="nav-bar">
      <div slot="center">购物车({{ length }})</div>
    </nav-bar>
    <cart-list></cart-list>
    <cart-bottom-bar></cart-bottom-bar>
  </div>
</template>

<script>
import CartBottomBar from "./childComps/CartBottomBar";

export default {
  name: "Cart",
  components: {
    CartBottomBar
  },
};
</script>
```

![1598691914066](F:\coderwhy_project\img_note\购物车底部板块的效果.png)

### 全选按钮的状态显示

- 在上面的购物车界面中，我们做了全选按钮的组件，但是这个全选按钮是默认选中的，现在我们需让它动态的与购物车中的商品联系起来，当我们把所有商品选中时，全选按钮被选中；当有一个甚至多个商品没被选中时，全选按钮是未被选中状态的；当购物车中没有商品时，全选按钮是未被选中的状态。

```vue
<!-- CartBottomBar.vue-->
<template>
  <div class="bottom-bar">
    <div class="check-content">
      <check-button :is-checked="isSelectAll" class="check-button"></check-button>
      <span>全选</span>
    </div>
    <div class="price">合计： {{totalPrice}}</div>
    <div class="calculate">去计算： ({{checkLength}})</div>
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
    isSelectAll() {
      //   如果购物车没有商品，那么全选按钮则不被选中
      if (this.cartList.length === 0) return false;
      //如果找到有一个没有checked属性的 就返回false
      //1.使用filter
      //   return !(this.cartList.filter(item => !item.checked).length);
      //2.使用find
      return !this.cartList.find(item => !item.checked);
    }
  }
};
</script>
```

![1598711230964](F:\coderwhy_project\img_note\购物车-全选按钮的状态.png)

### 购物车-全选按钮的点击效果

- 在上面的全选按钮的状态设置时，我们设置了每个商品与全选按钮的联系，但是现在我们点击全选按钮是否选中还是没有反应的，这需要用到@click.native来操作$el的状态，必须加native不然点击事件不会生效。

```vue
<!-- CartBottomBar.vue-->
<template>
  <div class="bottom-bar">
    <div class="check-content">
      <check-button :is-checked="isSelectAll" class="check-button" @click.native="checkClick"></check-button>
      <span>全选</span>
    </div>
    <div class="price">合计： {{totalPrice}}</div>
    <div class="calculate">去计算： ({{checkLength}})</div>
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
        //全部选中时 点击全选按钮 就把所有的商品按钮改为不选中
        this.cartList.forEach(item => (item.checked = false));
      } else {
        //部分或全部不选中 点击全选按钮 就把所有商品按钮改为选中
        this.cartList.forEach(item => (item.checked = true));
      }
        //用这种写法虽然可以，但是会问题，isSelectAll的状态会影响两个方法。
      // this.cartList.forEach(item => item.checked = !this.isSelectAll)
    }
  }
};
</script>
```

![1598757734010](F:\coderwhy_project\img_note\全选按钮的点击效果.png)

### 购物车-Vuex-actions返回Promise-mapActions

- 在商品的添加购物车时，我们在其他的软件中都会看见当商品添加到购物车时，页面的中间弹出一个添加购物车成功的字样，这个是toast(吐司)。我们要模拟这样的一个显示，先在控制台看能不能展示出这样的字样。
- 当添加商品时，如果购物车没有这件商品则显示添加到购物车成功，如果这件商品购物车有了，那么再添加的话就显示商品数量+1。由于添加商品的判断方法我们放在了vuex的actions的属性中，然而这个属性我们又是单独抽离出来成一个文件的，我们需要对这个actions属性中的addCart方法做判断，于是用到了Promise函数来进行包装。

```javascript
//actions.js
import {
  ADD_COUNTER,
  ADD_TO_CART
} from './mutation-types'
export default {
  addCart(context, payload) {
    return new Promise((resolve, reject) => {
      //   1.查找之前数组中是否有该商品
      let oldProduct = context.state.cartList.find(item => item.iid === payload.iid)
      //   2.判断oldProduct
      if (oldProduct) { //商品数量+1 之前添加到购物车了的
        //   oldProduct.count += 1
        context.commit(ADD_COUNTER, oldProduct)
        resolve('当前的商品数量+1')
      } else { //添加新的商品
        payload.count = 1
        //   context.state.cartList.push(payload)
        context.commit(ADD_TO_CART, payload)
        resolve('添加新的商品成功')
      }
    })
  }
}
```

- addCart方法中的if、else的判断分别代表了两种状态，通过resolve导出。在实际使用中通过.then来打印res 也就是resolve传递出来的数据。

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">
  </div>
</template>
<script>
//通过映射方式调用vuex中的actions属性中的方法
import { mapActions } from "vuex";

export default {   
   methods: {
    ...mapActions(["addCart"]),
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
      this.addCart(product).then(res => {
        console.log(res);
      });
    }
  }
};
</script>  
```

![1598761099027](F:\coderwhy_project\img_note\vuex中mapActions和Promise的结合使用效果.png)

### Toast组件的创建和使用

- 在上面我们提到了商品点击加入购物车时，页面中间会提示加入购物车成功，我们只是在控制台打印了一下，现在我们需要让打印信息出现在视图之上，然后过几秒自动消失；于是我们来创建这个组件，这个组件可能在许多项目中都用得上，所以我们把它放置在components文件夹下的common文件夹下的toast文件夹下创建Toast.vue组件。

```vue
<!-- Toast.vue-->
<template>
  <div class="toast" v-show="show">
    <div>{{message}}</div>
  </div>
</template>

<script>
export default {
  name: "Toast",
  props: {
    message: {
      type: String,
      default: ""
    },
    show: {
      type: Boolean,
      default: false
    }
  }
};
</script>

<style scoped>
.toast {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 8px 10px;

  color: #fff;
  background-color: rgba(0, 0, 0, 0.75);
}
</style>
```

- 这个组件的显示与隐藏和组件中的数据由我们的Detail.vue组件来控制，当商品添加成功了我们就可以更改Toast的显示和数据。

```vue
<!-- Detail.vue-->
<template>
  <div id="detail">
    <toast :message="message" :show="show"></toast>
  </div>
</template>

<script>
import Toast from "components/common/toast/Toast";
export default {
  components: {  
    Toast
  },
   methods: {
      addToCart() {
      // 1.获取购物车需要展示的信息
      ...
      // 2.将商品添加到购物车里
      // 通过映射的方式调用vuex中的actions中的方法
      this.addCart(product).then(res => {
        this.show = true; //添加购物车成功，则显示Toast组件
        this.message = res; //传递res 展示添加成功信息

        setTimeout(() => {
          this.show = false; //给个定时器，1.5秒之后Toast组件被隐藏
          this.message = ""; //清空数据
        }, 1500);
        console.log(res);
      });
    }
  }
};
</script> 
```

![1598773860843](F:\coderwhy_project\img_note\Toast组件的创建和使用.png)

### Toast-插件方式的封装

- 在上面的Toast的封装中，我们只是简单的封装了一下，在今后的复用过程中会很繁琐，因为父子组件之间要传递参数，设置方法。
- 现在我们以插件的方式来封装，就是相当于Toast组件为一个第三方模块，我们把它安装到Vue实例中，配置好它，用户使用时只需要设置它的的显示字样和显示时间就好了。

- 首先修改Toast组件，把要实现的方法写在组件里边。

```vue
<!-- Toast.vue-->
<template>
  <div class="toast" v-show="isShow">
    <div>{{message}}</div>
  </div>
</template>

<script>
export default {
  name: "Toast",
  data() {
    return {
      message: "",
      isShow: false
    };
  },
  methods: {
    show(message, duration) {
      this.isShow = true;
      this.message = message;

      setTimeout(() => {
        this.isShow = false;
        this.message = "";
      }, duration);
    }
  }
};
</script>

<style>
.toast {
   z-index: 999;
}
</style>
```

- 写好Toast组件的第三方模块的安装并挂载到视图的方式，该文件与Toast组件为同一文件夹下

```javascript
//index.js
import Toast from './Toast'

const obj = {}

obj.install = function (Vue) {
  // Vue.extend(trn)
  // console.log(Toast.$el)
  // document.body.appendChild(Toast.$el)
  // 1.创建组件构造器
  const toastContrustor = Vue.extend(Toast)

  // 2.new的方式，根据组件构造器，可以创建出来一个组件对象
  const toast = new toastContrustor()

  // 3.将组件对象，手动挂载到某一个元素上
  toast.$mount(document.createElement('div'))

  // 4.toast.$el对应的就是div
  document.body.appendChild(toast.$el)

  Vue.prototype.$toast = toast
}

export default obj
```

- 将toast安装到main.js中使用

```javascript
//main.js

import toast from 'components/common/toast'

//添加事件总线对象
Vue.prototype.$bus = new Vue()
// 安装toast插件
Vue.use(toast)

new Vue({
  render: h => h(App),
  router,
  store
}).$mount('#app')

```

![1598889483840](F:\coderwhy_project\img_note\Toast组件的高级封装展示效果.png)

### 解决移动端300ms的延迟

- 该程序运行在手机上时点击事件会有300ms的延迟，为了解决这个问题，我们使用第三方插件，fastclick。
- 第一步：安装：`npm install fastclick@1.0.6 --save`
- 第二步：导入、使用。 这都在main.js中进行配置的。

```javascript
//main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

//导入插件
import FastClick from 'fastclick'

Vue.config.productionTip = false

// 解决移动端300ms延迟
FastClick.attach(document.body)

new Vue({
  render: h => h(App),
  router,
  store
}).$mount('#app')

```

### 图片懒加载-vue-lazyload框架

- 在项目中，为了使用户体验更好，有些没浏览的商品其实可以不用加载的，这样会节约用户的流量，于是就有了图片懒加载这个理念，相应而生了vue-lazyload框架。
- vue-lazyload框架的使用
  - 下载：`npm install vue-lazyload@1.2.6 --save`
  - 导入、安装、配置(默认是不需要配置的)、使用。

```javascript
//main.js  导入、安装、配置都是在main.js中
import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";

import VueLazyLoad from "vue-lazyload";

Vue.config.productionTip = false;

//使用懒加载的插件
Vue.use(VueLazyLoad, {
  loading: require("./assets/img/common/yibo.jpeg")//图片在加载时候显示的默认图片
});

new Vue({
  render: h => h(App),
  router,
  store
}).$mount("#app");
```

- 使用的话就是在需要加载图片的地方把 `:src="xxximg"`换成`v-lazy="xxximg"`
- 我们先在首页的商品浏览中加入试试

```vue
<!-- GoodsListItem.vue-->
<template>
  <div class="goods-item" @click="itemClick">
    <img v-lazy="showImage" @load="imageLoad" />
    <div class="goods-info">
      <p>{{ goodsItem.title }}</p>
      <span class="price">{{ goodsItem.price }}</span>
      <span class="collect">{{ goodsItem.cfav }}</span>
    </div>
  </div>
</template>
```

- 下面是当图片在加载过程中显示的默认图片

![1598963371301](F:\coderwhy_project\img_note\图片懒加载-vue-lazyload框架的使用效果.png)

### px2vw-css单位转化插件

- 因为移动端有很多不同屏幕尺寸的设备，我们现在的项目的一些布局宽度和高度都是用px单位来写的，这样在有些设备显示的时候很可能出问题，为了让多端有很好的体验，我们也为了省事，于是用到了 postcss-px-to-viewport 这个插件。它可以快速的对px这个单位进行转化成vw，能够自定义想要转化的类和文件，让开发人员提升开发速度
- 由于这个插件只需要在开发时候使用即可 所以我们下载时 `npm install postcss-px-to-viewport@1.1.0 --save-dev`  加上了-dev ,表示发布阶段不需要这个插件，如果只有--save，表示在开发和发布阶段都需要用到这个插件。
- 直接去建立它的配置文件，在项目的根目录中新建postcss.config.js

```javascript
module.exports = {
  plugins: {
    autoprefixer: {},
    "postcss-px-to-viewport": {
      viewportWidth: 375, //视窗的宽度，对应的是我们设计稿的宽度
      viewportHeight: 667, //视窗的高度，对应的是我们设计稿的高度(也可以不配置)
      unitPrecision: 5, //指定'px' 转换为视窗单位值的小数位数(很多时候无法整除)
      viewportUnit: 'vw', //指定需要转换成的视窗单位，建议使用vw
      selectorBlackList: ['ignore', 'tab-bar'], //指定不需要转换的类
      minPixelValue: 1, //小于或等于`1px`不转换为视窗单位
      mediaQuery: false, //允许在媒体查询中转换`px`
      exclude: [/TabBar/] //必须是正则，匹配到的文件不需要转换
    }
  }
}
```

- 效果就是，没有指定的文件的px单位默认转换为vw了，指定了的类或者文件，它的单位还是保持原来的模样。

### nginx-项目在window下的部署

- 我们现在把自己的本机作为服务器，因为本机是window操作系统，于是下载的nginx使用window的步骤来部署服务器。
- 首先进入nginx官网下载，`http://nginx.org`找到stable的版本，这个是官网的稳定版本,然后点击下载，要注意，该软件解压到的路径不能含有中文，不然很可能出现后续问题。
- 我下载的版本是nginx-1.18.0 直接解压到当前文件夹，就会发现有个nginx.exe的执行文件，双击点击它就会启动服务器，但是我们目前还没有把项目部署到这个上面，所以得先把项目导入进来。
- 我们把项目先打包一下，`npm run build`，打包完成之后就可以在项目的dist文件夹中看见img文件夹、js文件夹和index.html
- 然后我们把dist整个文件夹放置到nginx-1.18.0文件夹中

![1599015069042](F:\coderwhy_project\img_note\nginx项目部署.png)

- 项目是导入成功了，但是我们还没有修改nginx服务器默认打开的文件路径，所以我们进入conf文件夹中，找到nginx.conf文件，使用记事本打开，修改它的默认打开根路径为dist。

![1599015230266](F:\coderwhy_project\img_note\nginx项目部署-2.png)

- 修改完成之后双击启动nginx.exe文件，启动服务器，打开浏览器，输入localhost回车，就会自动跳转到localhost/home ，也就是项目的首页，到这时候，我们的项目就成功在本机上部署运行成功了。

![1599015384007](F:\coderwhy_project\img_note\nginx服务器-项目部署成功效果.png)



**历时二十多天，跟着coderwhy老师学习了vue，万分感谢！**