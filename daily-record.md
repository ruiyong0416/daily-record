### 前言

努力的记录每日一个所学，希望能坚持下去！共勉！

#### 2022.06.30

##### 1. setTimout背后的秘密

> 参考文献：https://zhuanlan.zhihu.com/p/155752686

* setTimeout背后有一个 `4ms` 延迟的秘密。但是并不是所有浏览器厂商都遵循这个 `4ms` 延迟，各自有自己的一套延迟定义。只不过 `4ms` 是 `chrome` 团队经过多次研究觉得最为合适的一个值，后来也被制定为一个标准而已。

  > 1. 如果设置的 `timeout` 小于 0，则设置为 0
  >
  > 2. 如果嵌套的层级超过了 5 层，并且 timeout 小于 4ms，则设置 timeout 为 4ms。

* 但是浏览器不会允许 `0ms` 的存在，如果存在 `0ms`，会导致 `JavaScript` 引擎过度循环，造成阻塞，导致页面很容易出现奔溃，所以 `chrome` 的最低延迟是制定为 `1ms`（不同浏览器可能处理会不同）。所以下列代码在 `chrome` 允许会先输出1，然后是0，因为 `chrome` 的最低延迟就是 `1ms`。

  ```javascript
  setTimeout(() => console.log(1), 1)
  setTimeout(() => console.log(0), 0)
  // 1 0
  ```

##### 2. 浏览器-进程理解

> 参考文献：https://juejin.cn/post/6844904040346681358#heading-20

* 进程（process）是CPU资源分配的最小单位。（一般一个任务就是一个进程，比如打开一个浏览器就是启动一个浏览器进程）

* 线程（thread）是CPU调度的最小单位。（一般线程是建立在进程上的，即一个进程至少会有一个线程）

* 浏览器四大进程

  * 主进程（Browser Process）：负责浏览器界面显示与交互。各个标签的管理，创建和销毁其他进程。网络资源管理、下载等。

  * 第三方插件进程（Plugin Process）：浏览器是可以添加一些插件的，在使用插件时才会创建该进程。

  * GPU进程（GPU Process）：最多只有一个，用于3D绘制等。

  * 渲染进程（Renderer Process）：这也是浏览器内核在处理网页时重要的进程，它是多线程的：

    * GUI渲染线程：负责渲染页面（解析HTML、CSS、构建dom树和RenderObject树、布局、绘制等）。

      > 该线程与JS引擎线程互斥

    * JS引擎线程：处理js脚本语言（如V8引擎）。

      > 该线程与GUI渲染线程互斥

    * 事件触发线程：用于控制事件循环。

    * 定时触发器线程：定时器所在的线程。

    * 异步http请求线程：XMLHttpRequest连接后开启的线程。

* 浏览器需要解析HTML和CSS分别生成dom树和css树，再两则结合生成一个render树，那为什么不直接生成render树呢？

  * 因为浏览器在解析HTML构建dom树时，是不会被css阻塞的，所以可以并行处理，先解析HTML，再一边下载css资源。
  * 生成render树时，是需要等待css资源完成（无论成功或者失败）。所以如果要同时生成render树那将会比较消耗时间。
  * 还有一种可能，假设我render树不需要等css，那当我在构建时，css后面才请求回来，或者在中途请求回来，我又需要重头去查询对应的dom节点标记css信息，那将会是很消耗性能的操作。

#### 2022.07.05

##### 1.git基础操作

> 参考文献：https://backlog.com/git-tutorial/cn/intro/intro1_1.html

* git是一个分布式版本管理系统，是为了更好地管理Linux内核开发而创立的。且广泛应用于多人协同开发项目中。与之对应的版本管理有：svn等。

* git基本操作：推拉克隆

  * 推送（push）：将本地仓库分支推送至远程仓库
  * 拉取（pull）：拉取远程仓库分支并更新至本地
  * 克隆（clone）：克隆远程仓库到本地

* git窗口指令模式下，当中文没有显示而是被转译成 `ASCII` 码时，可以运行此命令。

  ````javascript
  $ git config --global core.quotepath off
  ````


#### 2022.07.20

##### 1. vue2新增钩子

> 参考文献：https://juejin.cn/post/7122089360354181134

* 配合 `Vue.use` 和 `Vue.mixin` 实现。当插件进行注册时(调用use)，必须提供一个对象且包含有 `install` 函数的属性，这里是注册的入口。在里面使用 `mixin`，以及配合 `$options` 属性。
* `$options` 是 `vue` 接收的选项，我们在通过 `$options.xxx` 来访问我们自定义的选项，如果存在就进行我们需要的操作，我这里是一个钩子，所以当它存在的时候我就去调用这个钩子。

##### 2.package.json符号认知

> 参考文献：https://blog.csdn.net/zy21131437/article/details/125762185

* package.json也是一个版本包管理器，对于package中的版本，遵循以下规范：**主版本 . 次版本 . 修复版本**。其中主版本的跨越可能是一种兼容或是不兼容的模式。
* `2.6.14`：这里前面没有修饰符号(其实是有一个0，但是0我们可以忽略不写)，这种情况就是锁定版本。即安装的时候只按照这个版本安装
* `^2.6.14`：这里有 `^` 符号。锁定主版本号，任意匹配次版本和修复版本，即能安装  `2.x.x，但不能安装 3.x.x`。
* `~2.6.14`：这里有 `~` 符号。锁定主版本和次版本号，任意匹配修复版本，即能安装  `2.6.x，但不能安装 2.7.x`。
* `latest`：安装最新的稳定版本。
* `*`：安装最新发布的版本，但不一定是稳定版本。
* 额外知识：为什么我们可以通过 `npm run xxx` 来启动项目，而不直接通过我们在 `package.json` 配置那段命令运行？
  * 因为如果直接允许时，我们的操作系统可能没有配置这些环境变量，所以可能会提示找不到什么什么，而 `npm` 会帮我们在下载依赖的时候，边解析 `bin` 文件，将所有的 `bin` 文件软链到 `node_modules/.bin` 目录下。所以实际上我们运行的时候，`npm` 是前往 `node_modules/.bin` 下寻找匹配的运行文件的。

#### 2022.07.21

##### 1.Cookie

* 当浏览器不禁用 `cookie` 时，浏览器会自动携带当前域的所有 `cookie`，合并进请求头，跟随请求到达服务器。
* 后端接口也可以在响应头里，直接为浏览器设置 `cookie`（在 `set-cookie` 字段中指定）。
* 跨域携带 `cookie`，需要前后端互相配合：
  * 前端设置 `credentials` 为true。
  * 后端需要将 `Access-Control-Allow-Credentials` 设置为 `true`，还有 `Access-Control-Allow-Origin` 不能设置为 `*`，必须指定白名单。
* 浏览器自动携带 `cookie` 需要满足以下3个条件：
  * 浏览器的域名与请求的域名一直（domain保持一致）。
  * 相同协议。如果协议不同，需要设置 `Secure` 为 `false`。
  * 请求路径与浏览器 `cookie` 的 `path` 要一致。即存储在浏览器的 `path` 为 `/test`，那么请求的路径必须是 `/test 或 /test/xxx`  才可以，若请求的是 `/not` 这一类不存在的 `path`，则不会被自动携带。

#### 2022.07.22

##### 1. this指向改变

* 因为 `js` 是在运行时，才会确定 `this` 的指向（箭头函数除外，箭头函数会在 `js` 预编译时就确定，静态确定）

* `call`：显示修改 `this` 指向，为立即调用。接收参数形式为多个 - call(this, param1, param1)。

* `apply`：显示修改 `this` 指向，为立即调用。接收参数形式为数组 - apply(this, [param1, param2])。

* `bind`：显示修改 `this` 指向，返回一个修改好 `this` 指向的新函数，为手动调用。接收参数形式为多个，且在 `bind` 绑定时可以传入，在调用返回的函数时也可以传入，传入参数会按顺序合并。

  ```javascript
  function fn(a, b) { console.log(a, b) }
  fn.bind({}, 1)(2) // 1, 2
  ```


#### 2022.07.23

##### 1. Hook理解

> 参考文献：https://juejin.cn/post/7066951709678895141

* `hook` 并不是只在前端所特有的关键词，在学界里是一直有的一个名称。`hook` 大体的意思是当程序运行到某一时机时，需要调取该时机的回调函数，这一过程就叫做 `hook`。

* 在 `vue2` 中，其实我们也可以使用 `hook` 来注册 `mounted` 生命周期，如下：

  ```vue
  <script>
  export default {
    created() {
      // 注册mounted的hook
      this.$once('hook:mounted', () => {})
      this.$on('hook:mounted', () => {})
    }
  }
  </script>
  ```

  我们可以通过 `$on、$once` 去监听所有生命周期钩子函数。

* `vue2` 中，还可以通过 `@hook:生命周期` 的形式，为组件绑定上对应的生命周期监听，如下：

  ```vue
  <template>
    <div>
      <my-component @hook:updated="handleUpdate" />
    </div>
  </template>
  ```

  当组件 `my-component` 更新时，我们绑定的 `handleUpdate` 函数就会被调用。

#### 2022.07.24

##### 1. vue2快速二次封装组件库

* 二次封装一些组件在我们的业务开发中也是很常见的。因为我们不需要从0到1去编写一个完整的组件，而是直接对组件库提供的已有组件进行二次封装，达到我们需要的效果。

* 但是当我们二次封装时，组件库提供一些 `props` 和 `events`，我们都需要重新声明然后传递吗？这样岂不是拉低效率啦。下面推荐 `vue2` 提供的两个全局属性，就可以解决上述的烦恼了

* `$attrs`：包含了父作用域中不作为 `prop` 被识别的 `attribute`。`class` 和 `style` 除外。当一个组件没有声明任何的 `prop` 时，在组件上添加的属性都会被 `$attrs` 接收(class和style除外)。再配合 `v-bind`，我们就可以继续把这些属性向下传递。

  ```vue
  <template>
    <div class="input-midlle">
      <t-input v-bind="$attrs" />
    </div>
  </template>
  ```

* `$listencers`：包含了父作用域中使用 `v-on` 注册的事件监听器（原生事件除外，即使用 `.native` 修饰器的）。再继续使用 `v-on`，我们就可以把事件向下传递。

  ```vue
  <template>
    <div class="input-midlle">
      <t-input v-bind="$attrs" v-on="$listeners" />
    </div>
  </template>
  ```

  这样，一个完整的基于组件库二次封装的 `input` 就优雅的诞生了。不再需要将组件库的每个属性和事件重复写多一次啦。

#### 2022.07.25

##### 1. historyAPI

> 参考文献：https://developer.mozilla.org/zh-CN/docs/Web/API/History

* `history` 是 `HTML5` 的新特性之一，且在我们 `vue-router` 使用中，也提供了两种导航模式，分别是 `hash` 和 `history`。为了网站的美观，`history` 是比较受欢迎的。
* 有时候我们有这样的场景，动态的更改导航栏，但不要页面刷新。这时候 `history` 的 `api` 就出场了，分别是 `pushState` 和 `replaceState`。
* `pushState`：会往历史记录栈顶中推入一个新的 ` url`，且不会更新页面。
* `replaceState`：会讲当前的记录移除并推入一个新的 `url`，来达到替换当前 `url` 的形式，且这个操作不会更新页面。

#### 2022.07.27

##### 1. CSS-BEM

> 参考文献：https://juejin.cn/post/7024699040436748295

* 在开发中，平时我们需要注重命名规范。因为命名的规范会增强我们的可读性。在 `css` 中，也有一套属于它们的命名规范，其中 `BEM` 就是其中的一种。

* `BEM` 拆解：

  * B：`Block` 表示 1 个块。

  * E：`Element` 表示 1 个元素。

  * M：`Modifier` 表示对块或元素的修饰符。

  * 例子如下图：

    * `navbar` 组件为 1 个 `block`。
    * 左 `navbar__left`，标题 `navbar__title`，右 `navbar__right` 三个区间就是 `element`。
    * 若有深色模式，则可以采用 `navbar--dark` 来修饰，即为 1 个 `modifier`。

    ![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/733b4695c5d4476881bfb5d18d2224ff~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

* `BEM` 的类名写法：
  * .b
  * .b__e
  * .b__e-m
  * .b-m

#### 2022.07.28

##### 1. 同源策略

> 参考文献：https://mp.weixin.qq.com/s/aJeWl5XdwuytI6ILYC62Dg

* 同源策略是浏览器的一个重要的安全策略。它用于限制一个 `origin` 的文档或者它加载的脚本与另一个 `origin` 的资源进行交互。其主要就是为了保护用户信息的安全，防止恶意的网站窃取数据。
* 有 `3` 种表现：
  * DOM层面：限制了来自不同源的 `Javascript` 脚本对当前 `DOM` 对象的读和写操作。（常见于iframe）
  * 数据层面：限制了不同源的站点读取当前站点的 `Cookie`、`IndexedDB`、`localStorage` 等。
  * 网络层面：限制了通过 `XMLHttpRequest` 等方式将站点的数据发送给不同源站点。

#### 2022.08.01

##### 1. vue中的keep-alive

* 在 `vue3` 中，当我们封装了 `keep-alive` 时，又需要用 `include` 进行组件名筛选。组件的定义会有两种方法：

  * `defineComponent`：这个写法和 `vue2` 一样，只需要使用 `name` 属性即可。

  * `setup` 语法糖：这个也有两种方式可以定义 `name`。

    ```vue
    // 方式一
    <script>
    export default defineComponent({
      name: 'xxx'
    })
    </script>
    
    <sciprt setup></script>
    
    // 方式二
    <scipt setup></script>
    // 此时为匿名组件，匿名组件的name为文件名
    ```

* `vue3` 使用 `<script setup>` 语法糖时，默认是匿名组件，匿名组件的 `name` 为文件名。假如有一个 `index.vue` 文件，其 `name` 为 `type: { __name: 'index' }`。

#### 2022.08.02

##### 1. css-var

* 在现代化开发中，我们频繁使用 `css` 预解析器，这是一个很帮助开发的工具，它也可以编写一些函数，以及声明变量。

* 现代浏览器也支持了 `css` 变量声明，如下：

  ```css
  :root {
    --white-color: #fff
  }
  ```

  `css` 变量一定要按照 `--` 开头的形式。

* 在 `vue` 开发中，有时候我们也可以灵活的时候 `css-var`，下面是一个动态切换颜色的例子：

  ```vue
  <template>
    <div :style="{ '--random-color': color }">
      <span clss="random-color"></span>
    </div>
  </template>
  
  <script setup>
  const color = ref('#fff')
  </script>
  
  <style scoped>
  .random-color {
    color: var(--random-color);
  }
  </style>
  ```

  以上只是简单的使用方法，具体可以根据情况优化。切记如果 `css-var` 没有绑定在 `:root` 上时，需要动态的绑定在某元素的 `style` 上。且变量的获取会就近获取。

#### 2022.08.03

##### 1. html布局

* 一般在设计 `pc` 官网等一些不需要自适应或者响应式的网站时，我们最好定好一个最小宽度，来防止宽度过小导致样式错乱的问题。

* 如上所说情况下，我们一般会嵌套 `2` 层的 `div` 做限制：

  ```html
  <style>
  .container {
    width: 100%;
  }
  .container-wrap {
    min-width: 1440px;
    margin: 0 auto;
  }
  </style>
  <div class="container">
    <div class="container-wrap"></div>
  </div>
  ```

* `css` 实现居中的方案除了 `position` 和 `flex`，还有一个 `margin: 0 auto;`。真是 `flex` 写多了，连最简单的 `margin` 都遗忘了。但是 `margin` 无法实现元素在父元素的中心点居中，只能确保元素在当前文档流的居中。

#### 2022.08.04

##### 1. TS - 初遇知识点

> 参考文献：https://www.typescriptlang.org/docs/handbook/2/typeof-types.html

* 在 `ts` 开发中，因为使用到了 `antd` 的 `Form-hook`，基于这里我是二封了 `hook`，但是遇到变量类型的问题，最终使用 `typeof` 来解决。

* 过程中不想去 `import type`，因为文件比较深处。想直接通过 `ts` 来自行推断，想了一些方法，最后发现直接使用 `typeof` 操作符，可以直接引用变量的类型：

  ```typescript
  const { validateInfos } = useForm(formData, formRules)
  
  type ValidateInfos = typeof validateInfos
  ```

* `ts` 毕竟是机器推断，有时候我们开发者比机器更清楚某个时刻的属性是否存在，所以 `ts` 提供了 `!` 操作符，在属性后面加上 `!` 表示此属性必定存在。

  ```typescript
  interface Person {
    name: string
    friends?: Person
  }
  
  const p: Person = { name: 'J' }
  p.friends!.name // 这样将断定friends属性存在
  ```

  其实上述例子应该使用 `可选链 ?` 更符合规范，但只是为了举个简单的例子！

#### 2022.08.09

##### 1. vuex

> 参考文献：https://v3.vuex.vuejs.org/zh/guide/modules.html#模块的局部状态

* `vuex` 是 `vue` 的周边插件。里面内置了 `module` 模块化的形式。这也是我们强烈建议在项目中使用的方式。

* `vuex: module`：模块化方便我们使用局部的 `state` 状态管理，分两种状态，如下：

  * 使用命名空间：需要将 `namespaced` 设置成 `true`，开启后 `getter`、`mutation`、`action`，都将会带有命名空间，使用时需要将模块名称带上，如下例子：

    ```javascript
    // moduleName - user
    export default {
      namespaced: true, // 开启命名空间标记
      action: {
        login() { ... }
      }
    }
    
    // 使用
    this.$store.dispatch('user/login')
    ```

  * 不使用命名空间：默认情况，`getter`、`mutation`、`action`，最终其实都会被注册成全局的属性，使用时直接调用即可。

    ```javascript
    // moduleName - user
    export default {
      action: {
        login() { ... }
      }
    }
    
    // 使用
    this.$store.dispatch('login')
    ```


#### 2022.08.16

##### 1. rebase

> 参考文献：https://www.jianshu.com/p/14f9eb70e99e

* 在开发中，经常使用 `git pull origin xxx`，但是使用 `pull`，不指定合并类型时，默认会选择自动 `merge` 成一个新的节点。如下，会关联两个父节点的信息。

  ![image-20220816221843848](C:\Users\Yo\AppData\Roaming\Typora\typora-user-images\image-20220816221843848.png)

* 如果项目中太多有这样的 `merge` 信息，其实是不方便溯源的，所以 `git` 有另一种合并策略，就是 `rebase`，它会将当前分歧出来的 `commit`，整个合并进目标分支的最后一个 `commit`。

  ![image-20220816224054632](C:\Users\Yo\AppData\Roaming\Typora\typora-user-images\image-20220816224054632.png)

* 使用方式：`git pull --rebase origin xxx`。使用 `rebase` 合并后，节点是干净的，只有一个父节点的信息。

  ![image-20220816223741365](C:\Users\Yo\AppData\Roaming\Typora\typora-user-images\image-20220816223741365.png)

* 如果有合并冲突时，可以手动打开文件先解决好合并冲突，然后执行 `git rebase --continue`，继续下一个 `rebase`。

* 如果需要放弃本次 `rebase`，可以执行 `git rebase --abort`。
