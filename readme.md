前端人去的网站
https://mp.weixin.qq.com/s/R-iLSEJS2GWsfmAHbnIYBw

在线工具
https://tool.lu/

好的面经
一尾流莺
https://juejin.cn/user/4099422807393901

山月
https://q.shanyue.tech/interview.html

待解决问题：

移动端页面适配方案 视频

TS应用

git 代码管理工具 不熟练 （分支 回滚）

伪类元素不熟练

js 原型和原型链

堆 栈

promise 

webpack 打包优化没有什么固定模式 一般常见的优化就是 拆包 分块 压缩等 也并不是对每个项目都适用 针对特定的项目 需要不断优化 不断调试

koa

长列表处理方式

数据请求从发起到接收数据之间发生了什么 可能问的是 三次挥手 四次握手

xss csrf 是什么 如何避免？
https://github.com/dwqs/blog/issues/68

Vue首屏渲染优化有哪些？
https://www.cnblogs.com/mianbaodaxia/p/10751453.html

http://www.inode.club/

https://pinia.web3doc.top/

搞清楚 问题是怎么出来的 然后反推 就解决了问题 z

git 本地分支 远端分支 开发在本地分支 暂且这么记

模块化开发
目录结构规范
设计规范
接口规范

  ajax有什么优点？有什么缺点？
  局部刷新
  缺点？

  同步和异步的区别？
  

  兼容性问题 
  请下载最新版本谷歌浏览器

性能优化

https://juejin.cn/post/6892994632968306702

好的一面是提升网站性能 坏的一面是配置麻烦 研究成本高 或者要遵循的规则太多
一个完整的HTTP请求需要经历
DNS查找 
TCP 握手 
浏览器发出HTTP请求 
服务器接收请求并发回响应
浏览器接收响应过程。

  怎样把网站升级到http/2
https://zhuanlan.zhihu.com/p/29609078
怎么才能让网站使用 HTTP2

简历篇
https://blog.csdn.net/qq_36538012/article/details/78318893
https://blog.csdn.net/weixin_41791345/article/details/102570890

链接: https://pan.baidu.com/s/1xQo4E4N1bc_wk_JbNdyahg 提取码: grw9 复制这段内容后打开百度网盘手机App，操作更方便哦

2022-4-3
阮一峰网络日志摘要
中国2021年度统计公报
中国进口最多的三样商品 集成电路 石油 铁矿石

交通通信 教育娱乐文化
国家统计局
http://www.stats.gov.cn/tjsj/zxfb/202202/t20220227_1827960.html

半导体 集成电路 芯片

2022-4-4
1. 软件的制作时间应该像蛋糕 如果耗费时间远远大于评估时间 十有八九是会有问题的
2. 蛋糕的体积很小 软件的规模也不应该很大 小体积可以很容易的进行试错 小体积意味着可以快速的大量迭代
3. 蛋糕有消费者 软件也应该有消费者

总之 把软件像成一块蛋糕 可以想通很多事情

bbb 说：

ZF估计气的很。。MMP这么多BATM，没一个能打的。。都TM在搞软科技。。

当然。。ZF也很愁。。以前自己牵头，被骗了几个Y

你说搞市场JJ嘛，资本家都不想啃硬骨头。。。

搞计划JJ嘛，自己人不仅不行，还窝里坑。。。

笑死了。。真的。。就没想过搭一个真正让人活跃不压抑的社会框架。。。ZG之悲哀命运轮回。。我估计这与中国人太聪明怒力卷有关系。。。就好像上帝推倒人类的巴别塔一样，中国人是人类当中最有机会建好巴别塔的。于是上帝就设计了这样一个坑给中国人，起到一种抑制平衡的作用。。想想万事都是如此（比如特别聪明的人情商一般就不高），真的很神奇。

M 说：

在互联网上，你的学习速度不是受限于信息的获取，而是受限于你忽视干扰的能力。你在网上关注的人是你的成功、健康和幸福的一个首要指标。

像是芒格那样谨慎 像马斯克那样做事。

2022-4-6
  如何消除一个数组里面的重复元素？
  原理就是对比两个数组 没有就放进去
  
  谷歌火狐浏览器兼容问题 
  基于谷歌浏览器开发调试
好多网站要求必须使用谷歌浏览器 最新版本所差无几

  如何给后端传递一个文件？
  https://zhuanlan.zhihu.com/p/120834588

  如何理解前端工程化
  https://mp.weixin.qq.com/s/Zvor5SBSy4RYtj_zmzhuAg

  promise.all 是为了解决什么问题？
  提供了 并行执行异步操作的能力 
  可能是 本轮数据操作依赖于上一轮数据
  
  promise.all 和 async/await 有什么区别？

  时过境迁
  前端开发
  https://zhuanlan.zhihu.com/p/351468283

自我学习
学会勇敢
学会独处 看书练字弹琴
更新技能 
培养爱好
用好时间
敢于尝鲜
减少依赖
复盘总结

2022-4-7
如何删除事件监听 一个元素绑定多个事件 怎么确认删除的是哪个？

匿名函数无法消除事件监听 只能通过实名函数才能

http是无状态协议 就是说这一次请求和上一次请求是没有任何关系的 互不认识的 没有关联的 
这种无状态的优点就是处理起来快速
不足就是 假如我想把 www.zhihu.com/login.html 和 www.zhihu.com/index.html 关联起来 
就必须使用某种手段和工具 

Cookie Session (这种方式基本被淘汰了 采用 localstroage/sessionstroage 源自 MDN Cookie)
Cookie 客户端容器
Session 服务器端容器
适用场景
我们登录网页 登录账号成功后 当我们把网页关闭 再次打开网页时候 我们依然处于登录状态 并没有随着关闭网页 状态消失 
Cookie有时效性
Cookie 是浏览器保存本地的文件内容
Cookie 常用于登录状态，用户资料等小文本

用户的登录成功后 会在服务器存一个session 同时发送给客户端一个Cookie
数据需要客户端和服务器同时存储
用户进行操作时 需要带上Cookie 在服务器进行验证

token
用户进行任何操作都要带上token
token只存在客户端 服务器接受数据后 进行解析

对比
token比Cookie 先进
https://www.cnblogs.com/moyand/p/9047978.html
https://segmentfault.com/a/1190000017831088

Cookie 验证流程 存量较小 存储登录状态 用户资料 小文本类 浏览器通常会自行添加 会造成csrf攻击漏洞 不超过4k
1. 用户输入登录凭证
2. 服务器验证凭据是否正确，并创建对话，然后会把数据存储在数据库中
3. 具有会话id的cookie被放置在浏览器中
4. 后续请求 服务器会验证会话id
5. 一旦用户登出 服务器和客户端会同时销毁该会话

Cookie + Session 客服增多 服务器器压力大 需要双向存储

token 验证流程 需要开发者每次手动添加
1. 用户输入登录凭证
2. 返回一个签名token
3. 客户端负责存储token
4. 请求带上token
5. 服务器端对token有效进行处理
6. 一旦登出 客户端销毁token

Symbol 是一种基本数据类型 
抛出错误demo

2022-4-8
js call apply bind 实现原理 未能理解
https://blog.csdn.net/qq_41999592/article/details/119244243

三次握手

从URL看到 页面 发生了什么？

DNS解析
发起TCP连接
发起HTTP请求
服务器处理请求并且返回HTTP报文
浏览器解析渲染页面
连接结束

为什么不是四次握手？采用两次握手行不行？

四次握手 并不能提高可靠性 而且也没有必要
两次握手 放置失效链接请求报文又传送到服务端 产生错误
第一次网络节点延迟
第二次正常连接 完成数据传输
第一次请求 几分钟后到 服务端收到后 同意连接 但此时 客服端根本不会理会 导致服务端资源浪费

A: 你准备好了进行通信吗？
B: 我准备好了，这是我们的口令，你拿着，请拿口令进行通信。
A: 我已收到口令，请你验证。

进行通信
