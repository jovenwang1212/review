# 1. 对于MVVM模式的理解?

MVVM 是 Model-View-ViewModel 的缩写。

**Model**代表数据模型，也可以在Model中定义数据修改和操作的业务逻辑(也就是后台)。
**View** 代表UI 组件，它负责将数据模型转化成UI 展现出来。
**ViewModel** 监听模型数据的改变和控制视图行为、处理用户交互，简单理解就是一个同步View 和 Model的对象，连接Model和View。
在MVVM架构下，View 和 Model 之间并没有直接的联系，而是通过ViewModel进行交互，Model 和 ViewModel 之间的交互是双向的， 因此View 数据的变化会同步到Model中，而Model 数据的变化也会立即反应到View 上。
**ViewModel** 通过双向数据绑定把 View 层和 Model 层连接了起来，而View 和 Model 之间的同步工作完全是自动的，无需人为干涉，因此开发者只需关注业务逻辑，不需要手动操作DOM, 不需要关注数据状态的同步问题，复杂的数据状态维护完全由 MVVM 来统一管理。

# 2.Vue的生命周期函数

**beforeCreate**（创建前） 在数据观测和初始化事件还未开始
**created**（创建后） 完成数据观测，属性和方法的运算，初始化事件，$el属性还没有显示出来
**beforeMount**（载入前） 在挂载开始之前被调用，相关的render函数首次被调用。实例已完成以下的配置：编译模板，把data里面的数据和模板生成html。注意此时还没有挂载html到页面上。
**mounted**（载入后） 在el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用。实例已完成以下的配置：用上面编译好的html内容替换el属性指向的DOM对象。完成模板中的html渲染到html页面中。此过程中进行ajax交互。
**beforeUpdate**（更新前） 在数据更新之前调用，发生在虚拟DOM重新渲染和打补丁之前。可以在该钩子中进一步地更改状态，不会触发附加的重渲染过程。
**updated**（更新后） 在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。调用时，组件DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。
**beforeDestroy**（销毁前） 在实例销毁之前调用。实例仍然完全可用。
**destroyed**（销毁后） 在实例销毁之后调用。调用后，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务器端渲染期间不被调用。

## 1.什么是vue生命周期？

​		答： Vue 实例从创建到销毁的过程，就是生命周期。从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、销毁等一系列过程，称之为 Vue 的生命周期。

## 2.vue生命周期的作用是什么？

​	答：它的生命周期中有多个事件钩子，让我们在控制整个Vue实例的过程时更容易形成好的逻辑。

## 3.vue生命周期总共有几个阶段？

​	答：它可以总共分为8个阶段：创建前/后, 载入前/后,更新前/后,销毁前/销毁后。

## 4.第一次页面加载会触发哪几个钩子？

​	答：会触发 下面这几个beforeCreate, created, beforeMount, mounted 。

## 5.DOM渲染在哪个周期中就已经完成？

​	答：DOM 渲染在 mounted 中就已经完成了。



# 3.Vue实现数据双向绑定的原理

​	vue实现数据双向绑定主要是：采**用数据劫持结合发布者-订阅者模式**的方式，通过**Object.defineProperty（）**来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应监听回调。当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 Object.defineProperty 将它们转为 getter/setter。用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。

vue的数据双向绑定 将MVVM作为数据绑定的入口，整合Observer，Compile和Watcher三者，通过Observer来监听自己的model的数据变化，通过Compile来解析编译模板指令（vue中是用来解析 {{}}），最终利用watcher搭起observer和Compile之间的通信桥梁，达到数据变化 —>视图更新；视图交互变化（input）—>数据model变更双向绑定效果。

​	下面是用js实现简单的双向绑定,大家把这个代码理解了,就差不多了,可以自己拿到编译器上面跑一下,

```
<body>
    <div id="app">
    <input type="text" id="txt">
    <p id="show"></p>
</div>
</body>
<script type="text/javascript">
	//创建空对象obj
    var obj = {}
    
    //调用Object.defineProperty()方法,obj表示要监听的对象,'txt'表示obj对象的txt属性
    Object.defineProperty(obj, 'txt', {
    	//get方法,获取值(这里不重要)
        get: function () {
            return obj
        },
        
        //set方法在obj的txt属性发生改变的时候触发
        set: function (newValue) {
            document.getElementById('txt').value = newValue
            document.getElementById('show').innerHTML = newValue
        }
    })
    
    //给输入框设置键盘按键弹起事件,当按键弹起之后触发,获取输入框的value,将其赋值给obj对象的txt属性
    document.addEventListener('keyup', function (e) {
        obj.txt = e.target.value
    })
</script>
```



# 4.**v-show和v-if指令的共同点和不同点?**

- v-show`指令是通过修改元素的`display`CSS属性让其显示或者隐藏
- `v-if`指令是直接销毁和重建DOM达到让元素显示和隐藏的效果



# 5.**如何让CSS只在当前组件中起作用**?

​	将当前组件的`<style>`修改为`<style scoped>`



# 6.keep-alive的作用是什么?

**keep-alive**是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染。
在vue 2.1.0 版本之后，keep-alive新加入了两个属性: include(包含的组件缓存) 与 exclude(排除的组件不缓存，优先级大于include) 。

使用方法

```html
<keep-alive include='include_components' exclude='exclude_components'>
  <component>
    <!-- 该组件是否缓存取决于include和exclude属性 -->
  </component>
</keep-alive>
```

参数解释
include - 字符串或正则表达式，只有名称匹配的组件会被缓存
exclude - 字符串或正则表达式，任何名称匹配的组件都不会被缓存
include 和 exclude 的属性允许组件有条件地缓存。二者都可以用“，”分隔字符串、正则表达式、数组。当使用正则或者是数组时，要记得使用v-bind 。

使用示例



```
<!-- 逗号分隔字符串，只有组件a与b被缓存。 -->
<keep-alive include="a,b">
  <component></component>
</keep-alive>

<!-- 正则表达式 (需要使用 v-bind，符合匹配规则的都会被缓存) -->
<keep-alive :include="/a|b/">
  <component></component>
</keep-alive>

<!-- Array (需要使用 v-bind，被包含的都会被缓存) -->
<keep-alive :include="['a', 'b']">
  <component></component>
</keep-alive>
```



# 7.**Vue中引入组件的步骤?**

1.采用ES6的`import ... from ...`语法或CommonJS的`require()`方法引入组件
 2.对组件进行注册,代码如下

```
// 注册
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
```

3.使用组件`<my-component></my-component>`



# 8.**在Vue中使用插件的步骤**?

1. 采用ES6的`import ... from ...`语法或CommonJSd的`require()`方法引入插件
2. 使用全局方法`Vue.use( plugin )`使用插件,可以传入一个选项对象`Vue.use(MyPlugin, { someOption: true })`





# 9.active-class是哪个组件的属性？

答：vue-router模块的router-link组件。

# **10.怎么定义vue-router的动态路由？怎么获取传过来的动态参数？** 

答：在router目录下的index.js文件中，对path属性加上/:id。  使用router对象的params.id

# 11.vue-router有哪几种导航钩子?

答：三种，一种是全局导航钩子：router.beforeEach(to,from,next)，作用：跳转前进行判断拦截。第二种：组件内的钩子；第三种：单独路由独享组件

# 12.scss(less)是什么？在vue.cli中的安装使用步骤是？有哪几大特性?

答：css的预编译。

使用步骤：

第一步：用npm 下三个loader（sass-loader、css-loader、node-sass）

第二步：在build目录找到webpack.base.config.js，在那个extends属性中加一个拓展.scss

第三步：还是在同一个文件，配置一个module属性

第四步：然后在组件的style标签加上lang属性 ，例如：lang=”scss”

有哪几大特性:

1、可以用变量，例如（$变量名称=值）；

2、可以用混合器，例如（）

3、可以嵌套

# 13.mint-ui是什么？怎么使用？说出至少三个组件使用方法？

答：基于vue的前端组件库。npm安装，然后import样式和js，vue.use（mintUi）全局引入。在单个组件局部引入：import {Toast} from ‘mint-ui’。组件一：Toast(‘登录成功’)；组件二：mint-header；组件三：mint-swiper

# 14.v-model是什么？怎么使用？ vue中标签怎么绑定事件？

答：可以实现双向绑定，指令（v-class、v-for、v-if、v-show、v-on）。vue的model层的data属性。绑定事件：<input @click=doLog() />

# 15.axios是什么？怎么使用？

答：请求后台资源的模块。npm install axios -S装好，然后发送的是跨域，需在配置文件中config/index.js进行设置。后台如果是Tp5则定义一个资源路由。js中使用import进来，然后.get或.post。返回在.then函数中如果成功，失败则是在.catch函数中

 

# 16、什么是RESTful API？怎么使用?

答：是一个api的标准，无状态请求。请求的路由地址是固定的，如果是tp5则先路由配置中把资源路由配置好。标准有：.get .post .put .delete

# 17.vuex是什么？怎么使用？哪种功能场景使用它?

只用来读取的状态集中放在store中； 改变状态的方式是提交mutations，这是个同步的事物； 异步逻辑应该封装在action中。
在main.js引入store，注入。新建了一个目录store，….. export 。
场景有：单页应用中，组件之间的状态、音乐播放、登录状态、加入购物车
![图片描述](https://segmentfault.com/img/bVOAAF?w=701&h=551)

**state**
Vuex 使用单一状态树,即每个应用将仅仅包含一个store 实例，但单一状态树和模块化并不冲突。存放的数据状态，不可以直接修改里面的数据。
**mutations**
mutations定义的方法动态修改Vuex 的 store 中的状态或数据。
**getters**
类似vue的计算属性，主要用来过滤一些数据。
**action** 
actions可以理解为通过将mutations里面处里数据的方法变成可异步的处理数据的方法，简单的说就是异步操作数据。view 层通过 store.dispath 来分发 action。

```
const store = new Vuex.Store({ //store实例
      state: {
         count: 0
             },
      mutations: {                
         increment (state) {
          state.count++
         }
          },
      actions: { 
         increment (context) {
          context.commit('increment')
   }
 }
})
```

**modules**
项目特别复杂的时候，可以让每一个模块拥有自己的state、mutation、action、getters,使得结构非常清晰，方便管理。



```
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
 }
const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
 }

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
})
```

 

# 18、自定义指令（v-check、v-focus）的方法有哪些？它有哪些钩子函数？还有哪些钩子函数参数？

答：全局定义指令：在vue对象的directive方法里面有两个参数，一个是指令名称，另外一个是函数。组件内定义指令：directives

钩子函数：bind（绑定事件触发）、inserted(节点插入的时候触发)、update（组件内相关更新）

钩子函数参数：el、binding

1.创建局部指令

```
var app = new Vue({
    el: '#app',
    data: {    
    },
    // 创建指令(可以多个)
    directives: {
        // 指令名称
        dir1: {
            inserted(el) {
                // 指令中第一个参数是当前使用指令的DOM
                console.log(el);
                console.log(arguments);
                // 对DOM进行操作
                el.style.width = '200px';
                el.style.height = '200px';
                el.style.background = '#000';
            }
        }
    }
})
```

2.全局指令

```
Vue.directive('dir2', {
    inserted(el) {
        console.log(el);
    }
})
```

3.指令的使用

```
<div id="app">
    <div v-dir1></div>
    <div v-dir2></div>
</div>

```

 

# 19.说出至少4种vue当中的指令和它的用法？

答：v-if：判断是否隐藏；v-for：数据循环出来；v-bind:class：绑定一个属性；v-model：实现双向绑定

# 20.导航钩子有哪些？它们有哪些参数？

答：导航钩子有：a/全局钩子和组件内独享的钩子。b/beforeRouteEnter、afterEnter、beforeRouterUpdate、beforeRouteLeave

参数：有to（去的那个路由）、from（离开的路由）、next（一定要用这个函数才能去到下一个路由，如果不用就拦截）最常用就这几种

 

# 21.请说下封装 vue 组件的过程？

答：首先，组件可以提升整个项目的开发效率。能够把页面抽象成多个相对独立的模块，解决了我们传统项目开发：**效率低**、**难维护**、**复用性**等问题。

然后，使用Vue.extend方法创建一个组件，然后使用Vue.component方法注册组件。子组件需要数据，可以在props中接受定义。而子组件修改好数据后，想把数据传递给父组件。可以采用emit方法。

# 22.你是怎么认识vuex的？

答：vuex可以理解为一种开发模式或框架。比如PHP有thinkphp，java有spring等。
通过状态（数据源）集中管理驱动组件的变化（好比spring的IOC容器对bean进行集中管理）。

应用级的状态集中放在store中； 改变状态的方式是提交mutations，这是个同步的事物； 异步逻辑应该封装在action中。

# 23.vue-loader是什么？使用它的用途有哪些？

答：解析.vue文件的一个加载器，跟template/js/style转换成js模块。

用途：js可以写es6、style样式可以scss或less、template可以加jade等

# 24.请说出vue.cli项目中src目录每个文件夹和文件的用法？

答：assets文件夹是放静态资源；components是放组件；router是定义路由相关的配置;view视图；app.vue是一个应用主组件；main.js是入口文件



# 25.Vue的路由实现：hash模式 和 history模式

**hash模式：**在浏览器中符号“#”，#以及#后面的字符称之为hash，用window.location.hash读取；
特点：hash虽然在URL中，但不被包括在HTTP请求中；用来指导浏览器动作，对服务端安全无用，hash不会重加载页面。
hash 模式下，仅 hash 符号之前的内容会被包含在请求中，如 [http://www.xxx.com](http://www.xxx.com/)，因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回 404 错误。

**history模式：**history采用HTML5的新特性；且提供了两个新方法：pushState（），replaceState（）可以对浏览器历史记录栈进行修改，以及popState事件的监听到状态变更。
history 模式下，前端的 URL 必须和实际向后端发起请求的 URL 一致，如 <http://www.xxx.com/items/id>。后端如果缺少对 /items/id 的路由处理，将返回 404 错误。**Vue-Router 官网里如此描述：**“不过这种模式要玩好，还需要后台配置支持……所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。”



# 26.Vue组件间的参数传递

**1.父组件与子组件传值**
父组件传给子组件：子组件通过props方法接受数据;
子组件传给父组件：$emit方法传递参数
**2.非父子组件间的数据传递，兄弟组件传值**
eventBus，就是创建一个事件中心，相当于中转站，可以用它来传递事件和接收事件。项目比较小时，用这个比较合适。

**3.VueX**



# 27.vue如何自定义一个过滤器？

html代码：

```
<div id="app">
     <input type="text" v-model="msg" />
     {{msg| capitalize }}
</div>

```

JS代码：

```
var vm=new Vue({
    el:"#app",
    data:{
        msg:''
    },
    filters: {
      capitalize: function (value) {
        if (!value) return ''
        value = value.toString()
        return value.charAt(0).toUpperCase() + value.slice(1)
      }
    }
})

```

全局定义过滤器

```
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

```

过滤器接收表达式的值 (msg) 作为第一个参数。capitalize 过滤器将会收到 msg的值作为第一个参数。





# 最后,一句话就能回答的vue面试题

**1.css只在当前组件起作用**
答：在style标签中写入**scoped**即可 例如：<style scoped></style>

**2.v-if 和 v-show 区别**
答：v-if按照条件是否渲染，v-show是display的block或none；

**3.$route和$router的区别**
答：$route是“路由信息对象”，包括path，params，hash，query，fullPath，matched，name等路由信息参数。而$router是“路由实例”对象包括了路由的跳转方法，钩子函数等。

**4.vue.js的两个核心是什么？**
答：数据驱动、组件系统

**5.vue几种常用的指令**
答：v-for 、 v-if 、v-bind、v-on、v-show、v-else

**6.vue常用的修饰符？**
答：.prevent: 提交事件不再重载页面；.stop: 阻止单击事件冒泡；.self: 当事件发生在该元素本身而不是子元素的时候会触发；.capture: 事件侦听，事件发生的时候会调用

**7.v-on 可以绑定多个方法吗？**
答：可以

**8.vue中 key 值的作用？**
答：当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。key的作用主要是为了高效的更新虚拟DOM。

**9.什么是vue的计算属性？**
答：在模板中放入太多的逻辑会让模板过重且难以维护，在需要对数据进行复杂处理，且可能多次使用的情况下，尽量采取计算属性的方式。好处：①使得数据处理结构清晰；②依赖于数据，数据更新，处理结果自动更新；③计算属性内部this指向vm实例；④在template调用时，直接写计算属性名即可；⑤常用的是getter方法，获取数据，也可以使用set方法改变数据；⑥相较于methods，不管依赖的数据变不变，methods都会重新计算，但是依赖数据不变的时候computed从缓存中获取，不会重新计算。

**10.vue等单页面应用及其优缺点**
答：优点：Vue 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件，核心是一个响应的数据绑定系统。MVVM、数据驱动、组件化、轻量、简洁、高效、快速、模块友好。
缺点：不支持低版本的浏览器，最低只支持到IE9；不利于SEO的优化（如果要支持SEO，建议通过服务端来进行渲染组件）；第一次加载首页耗时相对长一些；不可以使用浏览器的导航按钮需要自行实现前进、后退。

**11.怎么定义 vue-router 的动态路由? 怎么获取传过来的值**
答：在 router 目录下的 index.js 文件中，对 path 属性加上 /:id，使用 router 对象的 params.id 获取