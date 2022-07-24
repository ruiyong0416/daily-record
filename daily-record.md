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
