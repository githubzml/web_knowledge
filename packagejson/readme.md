<!-- https://juejin.cn/post/7023539063424548872#comment -->

一。必须属性

1. name: 项目名称
2. version ：表示该项目包的版本号 主版本号.次版本号.修订号
通常情况下 
修改主版本号 是做了功能性的改动
修改次版本号 就是新增了一些功能
修改修订号 就是修复一些bug

如果某个版本的改动较大，并且不稳定，可能无法满足预期的兼容性需求，就需要发布先行版本，先行版本通常会在版本号后面 通过"-"连接
内部版本（alpha） 公测版本（bate） 候选版本（rc release candiate）

![alt](./%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20220318093639.png)

二。描述信息
1. description： 用来描述这个项目包，它是一个字符串，可以让开发者在npm的搜索中发现我们的项目包。
2. keywords: 关键词，用来增加项目的曝光率。
3. author： 作者
4. contributors：贡献者
5. homepage：项目的主页地址
6. repository：存放仓库地址
7. bugs： 提交项目问题的地址

三。依赖配置
1. dependencies 生产环境所需要的依赖包

固定版本号 "react-scripts": "4.0.3", 
波浪号 ~4.0.3，表示安装4.0.x的最新版本（不低于4.0.3）
插入号 比如上面 react 的版本^17.0.2，表示安装17.x.x的最新版本（不低于17.0.2）

2. devDependencies 开发阶段所需要的依赖包

6. engines 说明具体的版本号要求

四。脚本配置
1. scripts 简单的命令 代替一些较长的命令 不好记得命令 多个命令
2. config 用来配置scripts运行时的配置参数

五。文件&目录
1. mian 指定加载的入口文件
2. brower 定义npm包在brower环境的入口文件
3. module 
4. bin 用来指定个个内部命令对应的可执行文件位置

六。发布配置
1. private 可以防止我们意外的将私有库发布到npm服务器 只需要将该字段设置为true 

6. license 指定版本的开源协议 开源协议表示其他人获得代码后拥有的权利，可以对代码进行何种操作，何种操作又是被禁止的 常见协议如下

MIT 只要用户在项目副本中包含了版本声明和许可声明，他们就可以拿你的代买做任何想做的事情，你也无需承担任何责任。
Apache 类似MIT 同时还包含了贡献者向用户提供专利授权的相关条款
GPL 修改该项代码的用户再次发源码或者代码时 必须公布他的相关修改

七。第三方配置
1. typings 指定TypeScript的入口文件
2. eslitConfig esint的配置可以写在单独的配置文件中
3. babel 用来指定Babel的编译配置
