### 特点

  开发环境基于浏览器对于 ES 的支持，这也就要求项目所有的模块必须是 ES Module。

  生产打包使用的是 rollup。

  目前定位主要是开发环境。

  相比较 webpack，vite 不需要将多个文件整合成一个或者多个 bundle 文件。


  vite 核心原理是通过 koa 服务拦截请求，返回相应的 ES Module ，其处理每一个模块使用的工具是 ESBuild，底层采用的 go 语法，速度非常的快。


  ES Module 在浏览器中运行时，浏览器只认相对路径，所以 vite 做一些特殊处理，便于拦截替换。

  .vue 文件也比较特殊，其包含 html、js 和 css，需要拆分开。先收到包含 script 逻辑的 App.vue 的响应，然后再解析 template 和 style。

  热更新 使用 websocket 实现，服务端监听文件的变化。

  优化：模块之间的依赖关系复杂的时候，会发送大量的请求。提取公共包 类似 dll-plugin 。


  更像替代 webpack-dev-server

  - 应对开发模式，拦截浏览器发出的 ES imports 请求并做相应处理。
  - 开发环境不需要打包，只要编译浏览器发出的 HTTP 请求对应的文件即可，编译采用的 esbuild，其底层用的 go 语法，速度更加快。（按需编译）


  模块路径的问题？node_modules 中的模块原来是由 webpack 来处理，现在需要 vite 添加前缀，便于在拦截请求时处理。

  热更新速度的问题：在 webpack 中，一旦发生某个依赖改变，将需要将其所处的 module 更新，并将新的 module 发送给浏览器执行。由于我们只打了一个 bundle.js，所以热更新的话需要重新打这个 bundle.js。随着依赖愈来愈多，则这个过程越慢。

  es-module-lexer 获取 imports 数组

  热更新：

  建立 websocket 通信，针对 Vue 组件本身的一些更新，都可以直接调用 HMRRuntime 提供的方法。其他的更新逻辑主要是通过 timestamp 来刷新缓存重新执行达到更新的目的。

  服务端监听文件变更，对于 Vue 文件需要编译，找出 template script style 的差异。

  客户端则是 vite 重新 App.vue 文件，注入相关的代码。