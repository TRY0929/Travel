## vue项目之去哪儿网

### 一、header区域

　　前面一些css和html布局就不多说，就说几个关键点。

+ 像背景色这种在全局里面都比较常见的颜色，可以打包放在另一个文件中，这样下次修改风格颜色时 维护起来就十分方便：
  + 在styles目录下新建一个 variables.styl 文件，里面定义一个变量为 $bgColor=背景色，然后在Header.vue的css中`@import '~@/assets/styles/variables.styl'`引入这个文件，然后就能使用 $bgColor代替了；
  + 之前也说到，@代表src目录文件，那每次都要找@/assets/styles这个目录，也可以设置一个快捷方式；**在build目录下找到webpack.base.conf.js文件**，发现里面有这样一句话`'@': resolve('src')`，所以我们也可以在底下加上，`'styles': resolve('src/assets/styles')`，实现一样的功能。

### 二、首页轮播图

#### 1）布局

+ 轮播图使用vue-awesome-swiper的一个插件，去GitHub上搜即可，有使用说明；
+ 记得引入的时候，在main.js中引入css文件，并且按照官方说明`Vue.use(VueAwesomeSwiper)`，然后在home.vue中也引入并且注册局部组件，最后在components目录下新建swiper.vue组件文件（和之前header过程一样，以后每次加入子组件都这样）
+ 添加完之后，轮播的话没有设置盒子的**固定宽高**所以在网络不好的时候可能造成抖动的问题，可以在轮播的最外面添加一个div 类设置为wrapper，在wrapper里设置：

```stylus
.wrapper
    overflow: hidden
    width: 100%
    height: 0
    padding-bottom: 36.64%
```

+ 下面的那里是**宽高比**，设置成这样不会有浏览器兼容的问题，最常使用。

+ 加上下面的小圆点，插件里面也实现好了，只需做修改即可（查看官方文档哈）；
+ 不过这里要注意的是，swiper.vue中的style设置了scope属性，也就是在这个文件中修改的样式不会影响到其他文件的样式，所以这里要用到穿透技术：

```stylus
.wrapper >>> .swiper-pagination-bullet-active
  background: #fff !important
```

+ 当然这里可以使用v-for来循环展示图片，就不用在template里写那么多东西了，循环的话就在swiperOptions里加上loop为true即可（查看官方文档即可知）；

+ 这里起到的是占位符的作用，这里的**padding-bottom和padding-top是相对于父元素宽度的比例**。

#### 2）逻辑

　　这里的实现原理和上面swiper几乎一致，就是把上面的swiper相关的标签先粘贴过来，再问题就来了，图标过多同样也要可以左右滑动，所以就要实现**分页**，那如何实现？

+ 借助**计算(computed)属性**，每8个就分一页，看代码：

```javascript
  computed: {
    pages () {
      const pages = []
      this.iconList.forEach((item, index) => {
        const page = Math.floor(index / 8)
        if (!pages[page]) {
          pages[page] = []
        }
        pages[page].push(item)
      })
      return pages
    }
  }
//循环的东西变了
<swiper-slide v-for="(page, index) of pages" :key='index'>
    <div v-for='item of page' class="icon" :key='item.id'>
```

+ 有个小技巧，实现**文字过多用省略号代替**：

```javascript
overflow: hidden
white-space: nowrap
text-overflow: ellipsis
```

+ 这段css样式可能很多地方都会用到，所以放到styles文件夹下的一个mixins.styl文件中，后续要用只需引入再调用即可(和之前的颜色值一样)

```javascript
//mixins.styl文件中
ellipsis()
  overflow: hidden
  white-space: nowrap
  text-overflow: ellipsis
//在Icon.vue中不要忘记引用
@import '~styles/mixins.styl'
//用的地方直接调用即可
ellipsis()
```

### 三、Ajax获取数据

#### 1）获取

　　页面是由一些子组件来构成的，每个子组件都有自己的数据，如果每个子组件都自己发送请求那就要发送很多次导致性能下降，所以干脆**就请求一次 把所有数据都获取到**。那在哪里请求呢？由代码结构可知，**Home.vue中包括了所有的子组件**，所以在那里请求是十分合适的。

+ vue提供了axios来专门用来获取Ajax数据，所以我们使用这个工具，先安装`npm install axios --save`，再在Home.vue中引用`import axios from 'axios'`；

+ **在Home.vue中定义生命周期函数mounted用来发送请求**，它调用函数，再往函数里具体添加内容即可：

```javascript
methods: {
    getHomeInfo () {
    	axios.get('/api/index.json')
    		.then(this.getHomeInfoSucc)
},
    getHomeInfoSucc () {

    }
},
mounted () {
	this.getHomeInfo()
}
```

+ 由于我们的项目还没有上线，要模拟请求就要将数据都存放到本地，这里我们**将数据放到static/mock/index.js**；
  + 为什么将数据放到static目录下？这是因为**只有static目录下的文件可以被外部访问到**，我们可以直接在网址`http://localhost:8080/`后加上`static/mock/index.json`，页面上就会显示Index.js里的内容；
  + 同时这是本地文件夹里的数据，我们不希望把这个内容提交到线上，所以在配置文件gitignore里加上 `static/mock`；
  + 这样一来，就要把上面getHomeInfo函数中的get参数改为`/static/mock/index.json`；

+ 但是在项目上线之后，并不再会使用本地模拟的地址，还是得写`axios.get('/api/index.json')`，但直接这么写上线之后是有风险的，所以我们**需要一个转发机制，将对以api开头的请求转发到本地文件夹/static/mock下**，这时就需要修改配置文件的内容了；
  + **webpack-dev-server提供了proxy代理的功能**，在config/index.js文件下，在dev(开发环境下)有一个proxyTable选项，编辑它：

```javascript
proxyTable: {
    '/api': {
        //还是请求这个网址不变
        target: 'http://localhost:8080',
        //只要以api开头 就转发到/static/mock文件夹下
        pathRewrite: {
            '^/api': '/static/mock'
        }
    }
},
```

+ 接下来就只要把数据放到index.json文件里即可。

#### 2）处理

　　刚刚已经直到如何获取到Ajax数据了，那现在如何传递到各个子组件？

+ **父组件给子组件传值是通过绑定属性来实现的，子组件接收是通过设置props属性**；
+ 那就可以对上面Home.vue中生命周期函数mounted里获取到的res进行处理：

```javascript
//父组件
data () {
    return {
      city: '',
      swiperList: []
    }
  },
methods: {
    getHomeInfo () {
      axios.get('/api/index.json')
        .then(this.getHomeInfoSucc)
    },
    getHomeInfoSucc (res) {
      res = res.data
      if (res.ret && res.data) {
        this.city = res.data.city
        this.swiperList = res.data.swiperList
      }
    }
  }
//子组件
  props: {
    city: String //用来规定数据类型
  }
```

#### 3）一点小问题

##### 初次渲染页面出现的不是第一页

　　这个问题主要体现在轮播图，由于一开始在父组件里定义的是空数组，所以开始创建的时候还没有元素，等到父组件接收到Ajax传来的数据再送给子组件时，出现的就不是第一页轮播图了；

+ 解决方法：让子组件渲染的时候判断以下，如果数据还没来就暂时不显示（用v-if），这里用子组件的计算(computed)属性比较好，避免在模板里出现逻辑性的代码：

```javascript
//swiper标签内加上v-if='showSwiper'
computed: {
    showSwiper () {
    	return this.swiperList.length
    }
}
```

### 四、配置路由(城市选择)

　　这里要实现点击城市展示城市页面的效果，之前也说过**路由就是根据不同的地址显示不同内容的东西**，这里通过配置路由来实现页面跳转。

+ 之前的内容都是在home页面下的，这次在pages文件夹下再创建一个city页面，用来写跳转到/city时的页面；
+ 路由配置都是router中的index.js文件中，和home一样多配置一个city路由；
+ City.vue中的内容写完后，要实现点击跳转，想到`router-link`标签，包裹城市那部分即可；
+ 加完标签之后发现城市那里字体颜色变化了，这时由于router-link默认在外面加了a标签，这里只要定义一个样式覆盖即可；
+ 同时这里有个小小的问题，在一切都准备好之后跳转也成功了，但是页面内容却无法显示，原因及解决方式：

如果你的 `exact` 设置为false，表示的是 非严格匹配；举个例子：

如果你的请求的路由是： '/a/b/c';那么他会被匹配到以下路由

1. `/`
2. `/a`
3. `/a/b`
4. `/a/b/c`

这里的 第4个路由是你的目标路由， 如果前3个路由的 `exact`都是 设置为 `false`; 并且路由的顺序是在 `4`的前面。那么前面3个的组件都会被渲染，并且默认的，会把 `2` 当作 `1`的子页面，`3`当作 `2`的子页面，

这样设计的目的，是为了实现路由的嵌套业务。所以你如果不希望这样，要么设置 `exact=true` ；要么注意顺序。

这种场景在模糊匹配的路由中也是存在的。

所以定义路由的时候，一般都是这样，

1. 长路由放在短路由前面，这里是说，路由前半部分相同的情况 `/a/b` 应该放在 `/a` 前面
2. 长路由放在模糊匹配的前面 `/a/b` 放在 `/a/:id`

> [原因及解决方案](https://segmentfault.com/q/1010000020475370/a-1020000020477908)

+ 后面的编写逻辑什么的和home页差不多。

### 五、city页面的一些知识点

#### 1）根据字母顺序显示城市，使用better-scroll插件

+ 不让页面根据自身的高度将盒子撑开，而是只显示页面能显示的部分：

```stylus
.list
    position: absolute
    top: 1.58rem
    left: 0
    right: 0
    bottom: 0
    overflow: hidden //关键
```

+ 然后查看GitHub中better-scroll的官方文档，找到使用方法与说明；

  + 先安装 `npm install better-scroll@next -S`

  + 引入 `import BScroll from '@better-scroll/core'`

  + 这里使用的话，参考 

    + > [better-scroll在vue中的使用]: https://zhuanlan.zhihu.com/p/27407024

> [better-scroll]: https://github.com/ustbhuangyi/better-scroll

#### 2）点击右边的快捷字母栏快速切换到不同字母页面

+ 这里涉及到**子组件间的传值**，可以像之前一样通过总线传值，也可 先从Alphabet传到City 再传到 List，也就是 **子-父-子**；
+ 子组件传值到父组件是通过出发触发事件，反之是通过属性；
+ List获得到当前点击的字母信息之后，**利用better-scroll自带的函数scrollToElement来跳到指定的元素处**；
+ 获取元素的话，这里在上面循环的时候加上ref属性，就能在函数里通过this.$refs来获得了，这里利用了watch属性来检测letter的变化：

```javascript
watch: {
    letter () {
      if (this.letter) {
        const el = this.$refs[this.letter][0]
        this.scroll.scrollToElement(el, 0, 0)
      }
    }
  }
```

#### 3）滑动右边字母栏切换到不同页面

+ 首先要给字母栏绑定 touchstart、touchmove以及touchEnd事件，处理手指拖拽事件，只有第一个事件被触发后面两个才能生效，所以这里要设置一个标识遍历touchStatus，每次在touchstart和touchEnd中被改变；
+ 在touchmove中：
  + 首先要获取当前手指在哪一个字母，通过 **当前的位置 减去 A字母的位置 再除以每个字母的高度，获得索引字母，再触发事件给父组件**（和上面一样）

```javascript
    handleTouchMove (e) {
      if (this.touchStatus) {
        const startY = this.$refs['A'][0].offsetTop //相对头部底部的高度
        const touchY = e.touches[0].clientY - 79 //相对屏幕顶部的距离 - 头部的高度
        const index = Math.floor((touchY - startY) / 20)
        if (index >= 0 && index < this.letters.length) {
          this.$emit('change', this.letters[index])
        }
      }
    }
```

#### 4）代码节流

+ 在我们拖动字母栏的过程中，其实touchmove事件被触发的频率十分高，从而handleTouchMove函数被执行的频率也就非常高，所以用下面这种方式来减少函数调用以提高代码性能，但又不会影响用户体验：

```javascript
//在数据项里设置了一个timer定时器    
handleTouchMove (e) {
      if (this.touchStatus) {
        if (this.timer) { //若上次还没执行完 则清除 来执行这一次
          clearTimeout(this.timer)
        }
        this.timer = setTimeout(() => {
          const touchY = e.touches[0].clientY - 79
          const index = Math.floor((touchY - this.startY) / 20)
          if (index >= 0 && index < this.letters.length) {
            this.$emit('change', this.letters[index])
          }
        }, 16)
      }
    },
```

#### 5）搜索框实现提示部分

　　要实现的功能为，在搜索框里输入一部分拼写或汉字，下面可以列出符合的城市名单；

+ 由功能可以看出，我们**需要实时监控搜索框中的内容**，所以想到**vue中的v-model双向绑定功能**，设绑定值为keyword；
+ 同时设置监听(watch)函数，当内容变化，我们就搜索cities(这里用到了Ajax的数据，所以父组件也要传值进来 属性)中符合搜索要求的城市放到数据项list中（同样使用了代码节流)；
+ 上面列表v-for循环list中的数据显示输出；

```javascript
  watch: {
    keyword () {
      if (this.timer) {
        clearTimeout(this.timer)
      }
      this.timer = setTimeout(() => {
        const result = []
        for (let i in this.cities) {
          this.cities[i].forEach((value) => {
            if (value.name.indexOf(this.keyword) > -1 || value.spell.indexOf(this.keyword) > -1) {
              result.push(value)
            }
          })
        }
        this.list = result
      }, 100)
    }
  }
```

+ 同时这里还要实现以下两个功能：

  + 查找不到相应内容的时候要显示 “没有找到匹配数据”：
    + 直接在ul里的最下面(城市的下面)加一个li，内容就是上面的提示，加上`v-show='hasNotData'`；

  + 搜索框里无内容的时候，搜索页面隐藏(这里好大一个坑，不隐藏虽然什么都不显示，但是后面的内容被遮挡住了，滑动不了！！！花了我八九个小时找原因，各种找better-scroll的原因，错怪了)：
    + 在外面的wrapper上加上 `v-show='hasNotData'`，这个函数返回`!this.list.length`，之所以定义一个函数，是要尽量避免在模板里出现逻辑，将逻辑都放到函数里去实现。

### 六、vuex

#### 1）概念与起源

　　Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

　　这个状态自管理应用包含以下几个部分：

- **state**，驱动应用的数据源；
- **view**，以声明方式将 **state** 映射到视图；
- **actions**，响应在 **view** 上的用户输入导致的状态变化。

但是，当我们的应用遇到**多个组件共享状态**时，单向数据流的简洁性很容易被破坏：

- 多个视图依赖于同一状态：
  + 传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。
- 来自不同视图的行为需要变更同一状态：
  + 我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

　　因此，我们为什么不**把组件的共享状态抽取出来，以一个全局单例模式管理**呢？在这种模式下，我们的**组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为**！

　　通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。

![vuex](https://vuex.vuejs.org/vuex.png)

#### 2）使用

+ 首先当然要安装vuex，创建一个vuex实例，并且将该子组件在根实例中注册，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 `this.$store` 访问到；
+ 修改state，必须让子组件通过dispatch调用actions里的方法，再在actions里通过commit调用mutations里的方法；
+ 这里也可以把actions删掉，直接让子组件通过commit调用changeCity；

```javascript
//store实例
export default new Vuex.Store({
  state: {
    city: '上海'
  },
  actions: {
     //ctx是上下文信息，函数体要通过它来获得commit方法
    changeCity (ctx, city) {
      ctx.commit('changeCity', city)
    }
  },
  mutations: {
    changeCity (state, city) {
      state.city = city
    }
  }
})

```

#### 3）通过vue router来跳转页面

　　在上面的点击事件处理函数里加上 `this.$router.push('/')`，即可实现，点击切换城市后立即跳转到home页面。

### 七、动态路由

　　每一个商品点开都有一个自己的详情页，跳转到的页面都不一样，这里通过动态路由的方式，根据商品自己的id来决定跳转到页面的路径。

+ 跳转是用router-link来实现， 即之前都是直接写，`to='路径'`，而现在改为`:to="'detail/' + item.id"`，加了个冒号，动态；
+ 同时再创建detail页面(和home city那边一样)
+ 这里有个小技巧，由于每次在最外层加上router-link后，vue都会自动渲染一个a标签，字体颜色也变化了，需要自己写样式进行覆盖。现在采用 直接将最外层标签替换为 router-link 标签，然后内部写一个 `tag='原本的标签名'`，**这样可以使router-link标签渲染成原本的标签，而不是直接变为a标签，就解决字体颜色变化的问题了**。

### 八、商品细节页的一些知识点

#### 1）将轮播当作全局可以使用的组件

+ 由于轮播这个功能也许很多页面都需要使用到，因此将其作为一个全局都可以拿到的组件，而不是放到某一个页面里；
+ 这里就在src目录下创建一个common文件夹，将gallary放到这个底下。

#### 2）让里面的盒子竖直居中(内部就一个盒子)

```stylus
//利用浮动 但是里面的盒子要有固定宽高(比)
display: flex
flex-direction: column
justify-content: center
```

#### 3）在使用swiper实现轮播图滑动不流畅

+ gallary部分，一开始是不显示画廊部分的，点击图片后显示，但是显示出来发现滑动不流畅，这是由于**在切换显示与不显示中 显示页的宽高发生了变化但swiper未重新计算**造成的，在swiper对象中添加如下两行：

```stylus
//将observe应用于Swiper的父元素。当Swiper的父元素变化时，例如window.resize，Swiper更新。
observeParents: true,
//启动动态检查器(OB/观众/观看者)，当改变swiper的样式（例如隐藏/显示）或者修改swiper的子元素时，自动初始化swiper。
observer: true
```

#### 4）头部渐隐渐显

+ 在最顶部的时候，头部header区域是不显示的，当往下滑动时header会随着距离增大而显示出来：
  + 给window绑定一个scroll事件(注意这个绑定要在页面布局的时候就绑定 所以利用生命周期函数mounted(页面被展示的时候执行)来实现);
  + 利用 document.documentElement.scrollTop 查看当前滚动距离，大于60则控制一个显示一个不显示(利用一个数据以及v-show来实现)；

+ 渐隐渐显
  + 当大于60时，利用样式来操控opacity的变化，即给元素绑定style属性，用一个数据里的对象来控制：

```javascript
const top = document.documentElement.scrollTop
this.opacityStyle.opacity = (top / 140) > 1 ? 1 : (top / 140)
```

#### 5）全局事件的解绑

+ 在刚刚的4中，detail页面给window绑定了scroll事件，之前的绑定都是在标签上，这次的事件绑定会影响到全局的滚动，所以当退回首页时这个事件绑定函数还是存在，因此需要解绑；

```javascript
//老师用的mounted和unmounted 但是文档中unmounted并没有，所以用destroyed
mounted () {
  window.addEventListener('scroll', this.handleScroll)
},
destroyed () {
  console.log('destroyed')
  window.removeEventListener('scroll', this.handleScroll)
}
```

+ 另外刚刚的top的值也有问题，在chrome上模拟并没有问题，但在手机上打开就无法显示有显示和隐藏的效果，将代码改为：

```javascript
const top = document.documentElement.scrollTop || document.body.scrollTop || window.pageYOffset
```

#### 6）递归组件实现列表

+ 递归 就是自身再调用自身，这里要实现多级列表，就不需要一层一层来写，如下：

```javascript
//在DetailList组件内
<div class="item-children" v-if='item.children'>
	<detail-list :list='item.children'></detail-list>
</div>
```

+ item.children这个数据存在的话 外面就将该数据当作list再传给自身，就可以实现和父组件一样样式的列表了。

#### 7）去掉页面缓存

+ 在App.vue根组件里使用了keep-alive标签，所以每次请求完一个页面之后会进行缓存，但在详情页里 每次请求的页面最后的id不一样，所以需要重新发送Ajax请求，使用以下语句即可，即加上exclude 这个页面不进行缓存：

```html
    <keep-alive exclude='Detail'>
    	<router-view/>
    </keep-alive>
```



#### 8）解决每次页面滚动随着页面切换会保留滚动进度的问题

在Router路由里，创建新路由的里面加上以下语句：

```javascript
  scrollBehavior (to, from, savedPosition) {
    return {x: 0, y: 0}
  }
```

+ 这样可以使得，每次切换到新的页面时都从页面顶部开始。

#### 9）动态渐隐渐现

+ 用类来实现渐隐渐现动画，(具体知识点移步之前的笔记)；
+ 这里在common文件新建一个fade文件夹来专门存放这个效果，因为和上面轮播一样，许多地方可能都会用到，就不将其放在某一个页面上。

### 九、代码部分编写完毕之后的调试工作

#### 1）前后端联调

+ 当前端代码与后端接口和数据都编写完毕之后，前端不再需要用自己模拟的mock文件夹里的数据，而是从后端直接接收数据过来；
+ 需要修改配置文件，config文件夹下的index.js文件，之前外面将proxyTable进行过修改，将对api的请求都换到/static/mock下，现在就可以直接将 pathRewrite删掉，同时将target改为后端真实的ip地址即可。

#### 2）真机测试

　　目前代码调试都是通过浏览器的手机模拟来调试，那真实到手机上打开该页面怎么办？

+ 首先是要修改配置项，先找到本机的ip地址：
  + Windows的cmd下输入ipconfig来查看本机ip地址；

+ 但手机或电脑直接访问该地址是无法打开页面的，因为webpack默认不允许用户通过ip地址来访问，所以要修改打包文件，package.json文件，在scripts下的start项后的命令里加上 `host 0.0.0.0`，即允许通过ip地址访问；
+ 同一局域网的设备通过输入刚刚查得的ip地址即可访问页面。

#### 3）项目上线

+ 命令行输入 `npm run build` 进行打包，文件夹内会多出一个dist目录；
+ 将dist目录放到后端服务器根目录下即可；
+ 若不想放到根目录，则要修改vue项目里的配置文件，config下的index.js，找到build下的assetsPublicPath，默认值是'/'，修改为 '/文件名'即可。

#### 4）异步组件按需加载

+ 当webpack按照默认的方式进行打包时，会将所有页面的js文件打包压缩到同一个文件里，所有当页面内容比较多时就会拖慢进度；
+ 要实现加载哪个页面就请求哪个页面的js文件(也就是按需加载 局部注册)：
  + 在router路由文件夹下的index.js文件里：

```javascript
component: Home
//改为
component: () => import('@/pages/home/Home')
//其余也是这么改
```

+ 但是注意，是要当局部页面比较庞大的时候才有必要这么做，不然每次切换一个新的页面都发一次http请求的代码比加载的代价要大得多。