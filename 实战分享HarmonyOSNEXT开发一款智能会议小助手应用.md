大家好，我是陈杨；一只会打代码的羊。最近在忙着全面升级我们的莓创图表组件，一直没有更新与分享相关的技术；等全面升级完成之后会给大家介绍一下做了什么升级，敬请期待！！

这次抽点时间出来给大家分享一下之前使用HarmonyOS NEXT开发了一款智能小助手APP整体的一个开发经验。我会大概分享一下整体技术架构以及功能实现介绍

## 产品架构

先来看看我们整个应用的技术架构。

1.  项目工程使用了鸿蒙最新的端云一体化模式，直接使用云存储、云数据库更省事。
2.  项目底层加入了豆包大模型，实现语言理解与总结、关键词训练、场景分析训练等多种场景
3.  利用鸿蒙众多新特性来实现多个功能，例如：华为账号一键登录、语音识别、音频服务、日常管理、文档扫描等等
4.  结合以上三种，整体项目输出：登录认证、会议管理、协同会议、语音识别、跨端流转文件等功能

除了我们使用鸿蒙特性实现了一些功能外面，我们还自己实现了笔记编辑引擎，能够自定义排版与布局自己的笔记内容，以及对笔记内容进行标注、加粗等等操作。接下来会一一介绍

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/b234383155f94e67b4f55ebff6ec7b00~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=DgZtzTob7aRttjhxSzL6mwsk3gs%3D)

## 产品主要功能

### 登录

我们整体框架采用的是端云一体化架构，而云开发（Serverless）提供了云认证服务，可以让我们快速的实现登录功能。这也是我们选择云开发的原因，他除了云认证之外，还有云函数、云存储、云数据库。

同时我们还加入了华为账号一键登录的功能，这是鸿蒙最新提供的华为账号服务，能够让用户快速登录。不单单在安全方面，在应用场景提供更多体验。例如：协同管理、流转管理等等相关场景。提升用户体验

两种登录模式的开发流程是这样的：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/6518864ffc5b4b7082c611a8e0d5e1a6~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=7k3MaFS0LzmELG1t5KNjQJ3EgeI%3D)

### 会议管理与日程管理

在会议管理方面支持创建应用、加入会议、预约会议三大功能。不管是创建应用还是预约会议，我们支持用户设置会议名称、会议类型、会议时长、会议日期等相关属性。

而在预约会议过程时，我们支持用户可以将会议添加到系统日历日程当中，设置提醒时间，实现系统化管理会议避免错过会议行程。

在加入会议过程中，我们支持用户通过输入会议口令或者扫码加入会议，进入会议则可以实时展示会议笔记与共同编辑笔记。

开发流程：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/de292b1c94d94c598e592b52e329f555~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=kepSIKj6PKkH7rNGYe6yQwehu1s%3D)

### 会议中心（语音录制+语音识别）

我们结合了鸿蒙的语音录制+语音识别相关的API来实现整个会议中心的内容，用户开启会议时可以通过实时录制将会议内容记下，在通过语音识别将语音内容实时转文字呈现出来。

开发流程：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/99f3db7725734437be41b74ed528be77~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=lyil%2FdoGrzRIXesYFuL5uRQ%2Fl54%3D)

### 会议纪要生成

在会议结束之后，在会议结束后，用户可以将录制生成的文本内容通过我们集成`豆包大模型`智能体总结出结构性的会议笔记跟会议摘要、笔记的重点内容。减少用户参与会议手动记录笔记的环节，让用户更加专注参与会议

开发流程：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/aaff509f8fa44b999da20057661e687a~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=ClebO5eo6MM6u2xCiCw%2BbgmCL8g%3D)

### 自研可视化编辑器

在鸿蒙没有富文本编辑器的情况下，我们自研了一套可视化编辑器引擎，可以支持拖拉拽进行添加或者排序、删除相关容器。比如输入框容器、图片容器、标题容器、轮播容器、排版容器等等。

给大家看一下整体设计模式：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d1733213eba44ea38b754f60272ffdda~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=9ggehX3h62HmcqxM9W5NP9gNz4I%3D)

目前还有部分功能没有完成，基本的都已经支持了。在坐标是我们的组件区与容器区，可以通过拖拽模式将组件添加到右边的呈现区。在标注区，我们实现了可以自定义给文字设置加粗与颜色效果，颜色选择器也是我们自研的。整体可以看一下具体效果图

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/b160a065e0ba4b13a5f5f8d2781e344d~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=B1KJkJDnFEi1djOa7FwbbMHLY14%3D)

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/aed67104fe99450bbb4b9cd31a382409~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071897&x-orig-sign=gnUj%2BQcRSun4jzXUR5LAEdo9VXs%3D)

## 结束

谢谢大家阅读，整体的一个实战分享就到此结束了，分享了一些主要功能的实现流程以及交互，如果某些功能你需要的可以找我单独交流，我这边也是一直在持续开发鸿蒙相关的产品。有其他问题也可以评论区跟我说，我看到都会一一解答。
