  什么是虚拟DOM?
虚拟DOM是DOM节点在JS中的一种抽象数据结构
之所以需要虚拟是因为浏览器中操作DOM代价高 频繁操作DOM 会产生性能问题

虚拟DOM的作用在每次响应式数据发生变化时引起页面的重新渲染 Vue对比更新前后的虚拟DOM 匹配找出尽可能少的需要更新的真实DOM 
从而达到提升性能的目的

虚拟DOM 实现原理主要包括3部分
用JS对象虚拟真实 DOM 树 对真实DOM进行抽象
diff 算法比较两颗DOM树的差异
pach 算法将两个虚拟DOM对象差异应用到真正的DOM树

  vue 组件间传值的 n 种方式 ?

1. props/$emit 实用 父子组件通信
2. ref 适用父子组件通信
4. EventBus ($emit/$on) 事件总线
7. Vuex 适用于 父子 隔代 兄弟组件通信

  什么是 SPA  有什么优点缺点
SPA 仅仅Web 页面初始化加载相应的HTML JS 和 CSS 
一旦页面加载完成 SPA不会因为用户的操作而进行重新加载或跳转
取而代之的是利用路由机制实现 HTML 内容的切换 UI 与用户的交互
避免页面的重新加载

优点： 
用户体验好，快，内容的改变不需要重新加载整个页面，避免不必要的跳转和重复渲染
有利于前后端职责分离，架构清晰，前端进行逻辑交互，后端负责数据处理
缺点：
初次加载耗时多，为了实现单页Web应用功能及显示效果 需要在加载页面的时候将 JS css 统一加载 部分页面按需加载
不利于SEO： 所有的内容都是在页面中动态替换显示 所以 在SEO上有着弱势

  Vue首屏渲染优化操作有哪些？
图片懒加载
路由懒加载
外部组件按需引入

vueCli打包后各文件的作用
vuecli 打包后文件详解
https://blog.csdn.net/weixin_42448623/article/details/108221403
Vue项目打包后dist文件目录详解
https://blog.csdn.net/qq_41611820/article/details/103699395

app.xxxx.js 当前应用程序的所有代码（业务代码）
mainfast.xxxx.js 为打包的代码作底层支撑 （为模块化，做底层支撑） 
vendor.xxxx.js vendor（提供者，第三方依赖包 vue，vue-loader，vuex...）

.js.map 文件
项目打包后 代码都是经过压缩加密的 如果运行时出错 输出错误信息无法准确得知是哪里出错。有了map就可以像未加密代码一样，准确的输出是哪一行那一列有错。
不生成.js.map设置
productionSOurceMap:false

  vue-router 有几种模式？
hash 和 history 有什么区别

早期的前端路由实现就是基于location.hash来实现的 location.hash 的值就是 url 中 # 后面的内容

比如下面的这个网站 https://www.word.com#search

location.hash 的值 就是 #search

hash 路由模式的实现主要基于下面几个特性：

1. ulr 中 hash 值是客户端的一种状态 也就是说 向服务器端发送请求时 hash 部分不会被发送
2. 可以使用hashchange 事件监听 hash 值的变化 从而对页面进行跳转渲染

history 实现原理
history.pushState() 和 replaceState()
这两个 API 可以在不进行刷新的情况下 操作浏览器的历史记录
前者是新增一个历史记录 后者是替换当前的历史记录

popState 事件监听 url 的变化
history.pushState() 或者 history.replaceState() 不会触发 popState 事件 我们需要手动触发页面跳转渲染

参考
https://blog.csdn.net/yexudengzhidao/article/details/101448168

  谷歌浏览器 把 https:/ 前缀给去掉了 为什么?
https://www.cnblogs.com/whatzblog/p/13338613.html
谷歌工程师 Emily Schechter 曾在 Chromium 反馈页面中提到：Chrome 团队一直将简易性、可用性、安全性作为衡量 UI 的标准。为了让 URL 能更好地被用户理解、移除那些容易让用户分心却并不重要的内容，我们计划从多功能框（地址栏）移除 https 和特殊子域名 www

解决方法: 安装这个插件
https://chrome.google.com/webstore/detail/suspicious-site-reporter/jknemblkbdhdcpllfgbfekkdciegfboi

考虑到大部分互联网协议都认同URL HTTP部分冗余，所以谷歌这么做似乎显得非常情有可原。

  vuex 有哪些属性
  State 定义了数据结构 这里设置默认的初始状态
  Getter 计算属性
  Mutation 唯一更改store 中状态的方法 必须是同步函数
  Action 用于提交mutation 而不是直接变更状态 可以包含任何异步操作
  Module 可以拆分 为多个store
