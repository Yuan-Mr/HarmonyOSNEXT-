大家好，欢迎来到莓创IT技术频道；我是陈杨。

> 今天给大家介绍一款我开发的动画制作软件：IF画。这款软件一开始是准备自己用来学习绘画跟学习制作动画视频的，如果学会成功了，自己就画画插画或者动画，然后投稿到各大短视频平台，做一位自媒体达人，就不再打代码了。但是当我写出这篇文章的时候，证明我失败了，我学不会，还是乖乖当一个程序员吧。

以前我是在web端实现的，这次我把他搬到了原生鸿蒙平台了，然后开放给大家使用，一起来学习，等到上架的时候（预计12月15日之前），大家可以在应用市场搜索一下：`IF画`。这里有一个`大福利`，只要你画出来的比我好，哪怕一点，都会`送你终身会员`，大家尽情的画起来。回归正题，这次在原生鸿蒙实现整个软件，我们采用了鸿蒙端云一体化的能力来实现整个软件的开发，说实话，端云一体化对纯前端同学来说，真的比较友好、方便，减少了很多学习成本。

所以这次我们来讲一下整体思路以及端云一体化是怎么交互的：

## 整体架构

从web迁移到鸿蒙，一开始在web的功能很简单，都是本地功能，简单的用服务器搭了一下生成动画视频的。而考虑到如果提供给大家一起使用的话，那就要整套服务都要支持了。所以我简单的设计了一下项目的架构以及云服务开发

简单的看两张图

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/a1ba89a592494888a31e8011dc3a435e~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=K22fpk6EVDtUjU%2FJm%2BZ3NYnUMtI%3D)

从项目架构图我们可以看出来，未来我们IF画软件的功能是很多的，包括`分布式协同`、`拖拽流转`、`分享`、`音频导入`等等。一期我们先搭建整个基础框架以及`绘画功能`、`视频生成`功能。这些功能都来源我们鸿蒙的特性，从而才能实现。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d84f4c9acaf142efb2a4718bcaf85f16~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=w0NrTlvs%2BUZQU3VU1YcVBrC9ss0%3D)

这张图主要是端云一体化的流程，我们主要用到了以下技术：

*   `云认证`：主要是实现华为账号一键认证。
*   `云函数`：主要是实现查询接口跟保存接口之类的
*   `云数据库`：主要是创建存储绘画数据与用户信息相关的表
*   `云存储`：这个很重要，以前用服务器的时候，上传下载存储静态图片麻烦的很。现在用云存储贼拉方便，都有对应的API，直接在端侧上传，下载都可以。这个**必须好评**

## 多端适配

我们IF画软件也适配了多端的能力，大家可以在平板也可以尽情的绘画您的作品。

![c3ea7224779c4acf774ced040fd5399.jpg](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/53f6c5e25bed4f1e8ed8ebd6f32a2070~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=DStvl7nGzV1mbzaJB7Qih0cttSQ%3D)

## 端侧功能介绍

讲完整体架构之后，我再讲解一下一期的核心功能吧，让大家对我们有一个基础的了解：

### 笔触与绘画算法

我们利用了图形算法实现了部分笔触，例如：圆珠笔、铅笔、水彩笔、马克笔等等。这个可以说是最重要的功能，如果没有多功能笔触的绘画工具，那就不是一个好工具。以后我们还会实现更加真实更加好用的笔触。

除了笔触之外，我们还而外开发了每种笔触的特性，比如宽度、流量、间隔、分散、蔓延、颜色等等属性，再配合我们的绘画算法，绘画算法我们主要考虑了几个因素：压力感应、速度控制等等。来实现我们整个绘画过程的真实感。

<img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/f9d4f9aac6e740388455c444ed7660e9~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=ufvCeMcI2DxxibBxpGF87uakbiE%3D" width="200">
<img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/92d23a167aad49119239e8686e72a673~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=mUNSlcI%2BTkICgsZeezn0mZYV4Ow%3D" width="200">

### 分层绘画模式与帧绘模式

我们软件主要有两种绘画模式

*   绘画模式：这个模式主要是用来绘画插画或者平面图的，他可以支持分层绘画，来实现更加复杂的绘画场景跟实现更多绘画细节。
*   动画模式：这个模式主要就是我们支持用户按照一帧一帧的内容去绘画，最终可以通过视频编辑器来生成动画视频。

<img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/7208b66f3a26457e9ecf8ea2e6948552~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=8ByQOsI0zvuOW7c5xWkDl%2BVgGTY%3D" width="200">

### 视频编辑器

当你使用动画模式来绘画的时候，你绘画好每一帧内容之后，可以进入视频编辑器，第一期的视频编辑器比较简单，只可以控制帧数，然后预览对应的效果。最后生成视频与保存视频。未来我们也会实现更多的功能，比如添加音频、控制每一帧的动画等等。

<img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/48dd44acc0cc495bbb6bbabbf03b65b2~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=%2BbJQYmzQ7hSYZTMRnVY0RhgqT48%3D" width="200">

## 云侧使用介绍

介绍之前先说一下感受以及使用的一些缺点。

首先感受这块：

*   简单易用，对初学者或者不懂后端的前端同学是比较友好的，毕竟可以选择nodeJs作为开发语言，对着文档写就好了，学习成本较低。
*   统一编辑器，端云一体化模式解决了开发需要下载多个不同的编辑器来开发对应的业务，减低了一定的学习成本。
*   无需部署，开发即用。如果要自己去搞一套后端服务的部署，还要考虑很多并发、负载等等场景，对于新手或者初学者来说肯定很难，而云服务就比较简单了，开发完成之后发布就可以直接用了。

缺点这块：

*   案例很少，对于新手来说可能不太友好。可能想开发某个功能但是不知道怎么在云函数实现
*   文档比较乱，文档上又有安卓又有快应用、鸿蒙，除了这些鸿蒙还分版本，分端侧跟云侧。我觉得可以归类整理一下，有时候我不知道这个功能到底是端侧开发好还是云侧开发好
*   文档举例不连贯，有些文档举例子经常找不到上下文，比如这个云函数上传云存储的，这个destiation属性初学者就不知道怎么生成。所以还是要继续优化一下
    ![0498b5df0d3ac3abdd7efaf2f389f2f.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d35e4460f5db48b48b9ae2da84856619~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=BbLwGlTHQDvSia%2FBrNHLZPRBLOo%3D)

感受跟缺点先讲到这里，我来分享一下我的开发案例

### 预加载

这次云侧加了一个很好的功能，也变向了解决从0到1的存活问题。这样就提高首页加载的速度了，就再也不怕加载超时或者加载不出来了。而且配置也很简单，在AGC配置平台配置要缓存的云函数，然后在代码对应的云函数加上`loadMode: cloudFunction.LoadMode.PRELOAD`就可以了，简单易用

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/e19ccbb4315040b182971767f73b1082~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=ZAhOhMgNJ%2FrbVrRQna5tG2ZA8ho%3D)

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/18e4783587b645efa2d64c390770e774~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=cBWqCuiJzulG4WI78HcHsD6am78%3D)

### 云认证

云认证首先要在主入口注册，以前是不需要的，以前创建一体化项目的时候就会自动携带，现在需要我们手动在EntryAbility.ets下注册。代码如下：

```js
import auth from '@hw-agconnect/auth';
import buffer from '@ohos.buffer';
export default class EntryAbility extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    let file = this.context.resourceManager.getRawFileContentSync('agconnect-services.json');
    let json: string = buffer.from(file.buffer).toString();
    auth.init(this.context, json);
    hilog.info(0x0000, 'testTag', '%{public}s', 'Ability onCreate');
  }
}
```

**这里有一个需要注意的点，agconnect-services.json需要放在这个目录下面：项目根目录/AppScope/resources/rawfile/agconnect-services.json，而rawfile自己手动创建，agconnect-services.json是从agc上面项目管理获取即可，这里是跟以前的不太一样的地方**

### 端侧云存储

以前的云存储，我都是放在云函数实现的，但是这次新版本之后，云函数调用的方法data已经不支持很长的字符串了，我这边需要传base64去保存，导致一直报错，所以我这次是换成端侧云存储，具体实现方式如下：

```js
import { cloudStorage } from '@kit.CloudFoundationKit';
import common from '@ohos.app.ability.common';
let context = getContext(this) as common.UIAbilityContext;

// 将base64存在沙箱中
async writeFile(data: string): Promise<string> {
  let uri = ''
  let filesDir = getContext().filesDir;
  let filePath = ''
  const name = `${Date.now()}.png`
  try {
    filePath = context.cacheDir + `/${name}`;
    uri = fileUri.getUriFromPath(filePath);
    let file = fileIo.openSync(filePath, fileIo.OpenMode.READ_WRITE | fileIo.OpenMode.CREATE);
    const reg = new RegExp("data:image/\w+;base64,")
    const base64 = data.replace(reg, "");
    const dataBuffer = buffer.from(base64, 'base64')
    await fileIo.writeSync(file.fd, dataBuffer.buffer);
    fileIo.closeSync(file);
  } catch (Error) {
    console.log('Error.code', '----->👉', JSON.stringify(Error));
  }

  return "internal://cache/" + name

}

update () {
    const previewUrl = 'down/xxxx' // 这里是你云存储创建的目录down，然后后面是你要上传的文件名称
    const bucket: cloudStorage.StorageBucket = cloudStorage.bucket('xxxx'); // 这里是你云存储的实例
    // ArkUI上下文
    await bucket.uploadFile(getContext(this), {
      localPath: await this.writeFile(item.imagUrl),  // 本地文件路径
      cloudPath: previewUrl     // 云侧文件路径
    }, (err: BusinessError, task: request.agent.Task) => {
      // 这个回调函数一定要写，不然上传不了
      if (err) {
        console.error(`Upload file failed! Code: ${err.code}, message: ${err.message}`);
        return;
      }
      task.on('progress', (progress) => {
        console.info(`on progress ${JSON.stringify(progress)}`);
      });
      task.on('completed', (progress) => {
        console.info(`on completed ${JSON.stringify(progress)}`);
      });
      task.on('failed', (progress) => {
        console.error(`on failed ${JSON.stringify(progress)}`);
      });
      task.on('response', (response) => {
        console.info(`on response ${JSON.stringify(response)}`);
      });

      // start task
      task.start((err: BusinessError) => {
        if (err) {
          console.error(`Failed to start the uploadFile task, Code: ${err.code}, message: ${err.message}`);
        } else {
          console.info(`Succeeded in starting a uploadFile task.`);
        }
      });
    });
}

```

**这里需要注意的点就是：bucket.uploadFile函数有三个参数，都是必填的，不然上传不了。我一开始以为第三个可以不用写，然后一直没上传成功，还以为是没获取到本地，最后才发现是没写回调，大家注意了。**

### 云函数的依赖

创建一个云函数的步骤：打开你的项目云侧代码，找到cloudfunctions文件，然后右键，就可以看到new --> cloud Function，点击即可创建。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/4b071d2241b24ea9a4607ca24231a860~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=GUJwu9Y2e%2BN9ilrJv3UuShX09Ls%3D)

然后这里如果你要装第三方依赖或者云服务器相关的依赖，要在对应的云函数目录下安装，如果你在整个项目里面安装依赖的话，上传的时候，就会打不到依赖，云函数调用就会报错，所以要避免这点

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/6ae90bb8760f4a69a1cf65dc3b2f4dc0~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071976&x-orig-sign=SdqzU1%2F2ZuWGp%2FL6AKdrWcHTTCY%3D)

### 云函数调用云数据库

首先先安装以下四个依赖，我不知道最新的有没有变哈，这是我从API9的时候就开始用的了

```js
"@agconnect/cloudstorage-server": "^1.0.1",
"@agconnect/database-server": "^1.0.7",
"@hw-agconnect/common-server": "^1.2.4",
"@hw-agconnect/database-server": "^1.0.21",
```

然后在云函数里面注册云数据库

    const clouddb = require('@hw-agconnect/database-server/dist/index.js')
    import {AGCClient, CredentialParser} from "@agconnect/common-server"
    const path = require('path');
    // 加载凭证文件
    let client_name = "./db/agc-apiclient-chen.json"; // 你的认证json，从agc获取
    let client_path = path.join(__dirname, client_name);
    let credential = CredentialParser.toCredential(client_path);
    let agcClient;
    try {
      agcClient = AGCClient.getInstance();
    } catch {
      AGCClient.initialize(credential);
      agcClient = AGCClient.getInstance();
    }

    clouddb.AGConnectCloudDB.initialize(agcClient);
    const agconnectCloudDB = clouddb.AGConnectCloudDB.getInstance(agcClient);
    //云数据库区块
    const zoneName = 'MeichuangEdit';
    const cloudDBZoneConfig = new clouddb.CloudDBZoneConfig(zoneName);
    const mCloudDBZone = agconnectCloudDB.openCloudDBZone(cloudDBZoneConfig);

这里注册之后就可以使用云数据库的查询、保存、更新、删除等等功能了，这个我放在后面详细的讲吧，东西很多。然后还有一个要注意就是，判断是否已经实例化了，就是上面代码的try{}catch{}，以前是不需要的，新版本需要了，我也是查了很久资料才知道，文档上是没写的，所以大家要注意避开。

## 结束

我们今天的分享就讲到这里吧，云服务有机会的话我会单独出一套系列的文章给大家分享，我还注册了b站的讲师，也可以给大家录课，但是就是太忙了，抽不出身来去做这些事情，等以后吧，相信不会太久，大家可以先去关注我的B站，B站搜索：莓创-陈杨。有技术问题都可以找我。

还有大家不要忘记了我们这个软件的福利哦，到时候记得来参与，鸿蒙原生应用市场搜索：IF画。画出你的作品，只要画的比老板好，就送会员，最高奖励终身会员。快充吧
