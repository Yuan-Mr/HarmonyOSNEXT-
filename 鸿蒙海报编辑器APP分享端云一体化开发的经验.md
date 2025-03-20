## 前言
在我工作的日常中，经常会用一些画图编辑器，简单设计一些页面原型。而在去年低代码很火的时候，我在公司就开发了一款大屏可视化编辑器，可以通过拖拉拽生成网页的一个工具。后面也想着自己也开发一个画图的编辑器，可以用来自己平时做图或者设计海报之类的。而这次我将使用鸿蒙ArkTs来开发这款海报编辑器APP，顺便分享一下我这次的开发经验。

## 项目架构与开发流程

我这次项目功能架构以及整体开发流程如下：

**项目功能结构**

![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/bf74d548-93af-423a-931b-6018a380e76c/image.png 'image.png')

**整体开发流程**


![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/59c7a82e-f434-4fc7-9e32-cc339a4e7882/image.png 'image.png')


这次开发我使用了鸿蒙的端云一体化的开发模式进行整个项目的构建。说实话：之前我都是自己写后端代码，开发流程确实比较繁琐。而云开发模式就很方便了，端侧与云侧同时开发，无需搭建服务器，工具成本低。这点我还是很喜欢的。

## 端云一体化工程讲解

端云一体化的工程我也是第一次开发，相信很多伙伴也没开发过。我这次用的是官方提供的云模板工程，我大概给大家讲解一下：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b3c7e7e1a6754606bc427259863f6453~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=752&h=1478&s=210739&e=png&b=3a3d3f)

整体结构很简单，使用起来也比较方便。所有的操作DevEco Studio开发工具都集成了。

而在讲解之前，使用云模板创建项目之前需要在华为开发平台创建项目，具体创建流程可以到官方文档查看，地址我也放在下面：https://developer.harmonyos.com/cn/docs/documentation/doc-guides-V3/agc-harmonyos-clouddev-agcapp-0000001439436876-V3#section397317130308

**注意：记得是创建项目哦，不是应用。我一开始就搞错了**

### 创建表与方法

1. 比如你要新建一张数据库表，只需在`clouddb`云数据库入口文件点击右键，点击new，然后你选择需要创建什么。
![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/333b5f14-fc38-4344-ad0f-520d9140927d/image.png 'image.png')
> 而我比较喜欢在开发平台创建，比较方便，傻瓜式操作。大家也可以看看官方文档：https://developer.huawei.com/consumer/cn/doc/AppGallery-connect-Guides/agc-clouddb-createfirstclouddb-0000001569588629

2.比如你要创建一个云函数方法，也是只需在`cloudfuntion`云函数入口文件点击右键，点击new，然后你直接创建一个对应的云函数即可。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8423e4b8ad9e4b65b6499f81c32374aa~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1182&h=494&s=190683&e=png&b=3c3f41)

## 开发函数与数据库的使用

> 由于第一次使用云函数与云数据库，一路上磕磕碰碰。所以我打算将自己经验都分享给大家，希望能帮助大家。

### 开发云函数

在创建云函数之后，代码结构如下：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c1b93c9fae344f7bc1f7ebbba390e43~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=482&h=242&s=27694&e=png&b=3c3f41)

其中有一个ts文件，这个就是我们的函数入口了，其他的我们都暂时不用动。这里我默认的是nodejs开发模式以及http请求模式。如果你们需要修改配置可以看一下官方文档：https://developer.huawei.com/consumer/cn/doc/AppGallery-connect-Guides/agc-cloud-function-start-from-here-0000001512489692

ts文件里面默认有一个主入口方法：

```
let myHandler = async function (event, context, callback, logger) {
  // 获取你的参数
  const body = event.body ? JSON.parse(event.body) : event

  // 写你的业务代码

  callback({
      code: 200,
      data: [],
      msg: '提示内容'
  });
};

export { myHandler };
```

-   myHandler：入口方法名称。
-   event：调用方传递的事件对象，JSON格式。具体内容请参见[event对象](https://developer.huawei.com/consumer/cn/doc/AppGallery-connect-Guides/agc-cloudfunction-trigger-event-0000001620581529)。
-   context：函数运行时上下文对象，封装了日志接口、回调接口、环境变量env对象等。
-   callback：事件处理结果。
-   logger：记录日志。
> 这里需要注意的是：获取参数的方式，我在本地调试的时候，event就是参数对象了，但是发布到云服务器之后，端侧传递的参数需要通过event.body来判断是否存在，然后将数据JSON化。也不知道是不是我用的不对，但是目前我是遇到这个问题了。

接下来我讲解一下云函数调用云数据库与云存储的相关步骤。详细步骤如下：

### 云函数使用云数据库

**1. 云数据库注册认证**
> 在使用云数据库的所有方法时，都需要先注册认证哈
```js
const path = require('path');
const clouddb = require('@hw-agconnect/database-server/dist/index.js')
const { AGCClient, CredentialParser } = require("@hw-agconnect/common-server");
const db_demo = require('./db/bd_demo') // 从开发者平台下载对应的js配置文件，放在对应的函数目录根目录下

// 加载凭证文件，凭证文件也是在开发者平台下载，具体看我的截图
let client_name = "./db/agc-apiclient-chen.json";
let client_path = path.join(__dirname, client_name);
let credential = CredentialParser.toCredential(client_path);
AGCClient.initialize(credential, "clientCN", "CN");

let myHandler = async function (event, context, callback, logger) {
  // xxxx
};

export { myHandler };
```

1.1 下载加载对象类型文件
![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/41f8254c-fd15-44a7-8aeb-5ee6940e351e/image.png 'image.png')
1.2 将上面下载的文件引入到云函数目录中
![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/9f2f7cd2-4d04-47e3-83ba-5513e2f9ffb3/image.png 'image.png')
1.3 下载凭证文件
![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/38b8d6fe-8443-48e3-8c7b-9d9da9f36fac/image.png 'image.png')

**2. 初始化云数据库实例以及打开存储区**
```js
// 接着加载凭证文件的相关代码下
const agcClient = AGCClient.getInstance("clientCN");
clouddb.AGConnectCloudDB.initialize(agcClient);
const agconnectCloudDB = clouddb.AGConnectCloudDB.getInstance(agcClient);
//云数据库区块，获取方法看以下截图
const zoneName = '区块名称';
const cloudDBZoneConfig = new clouddb.CloudDBZoneConfig(zoneName);
const mCloudDBZone = agconnectCloudDB.openCloudDBZone(cloudDBZoneConfig);
```
2.1 获取区块名称
![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/43360b47-9ac2-4c83-be19-7c0250bf4990/image.png 'image.png')

**3. 使用云数据库的方法**

3.1 云数据库-新增数据
> 需要注意的是：表名称，直接写对应的字符串名称就好
```js
// 上面是注册、加载凭证、打开存储区的代码

let myHandler = async function (event, context, callback, logger) {
  // 获取你的参数
  const body = event.body ? JSON.parse(event.body) : event

  //表名称也是通过开发者平台获取即可，字符串模式
  const bookInfo = clouddb.CloudDBZoneGenericObject.build('表名称')
  // 添加属性对应的数据，按照自己定义的字段格式去赋值
  bookInfo.addFieldValue('字段名称1', '数据')
  bookInfo.addFieldValue('字段名称2', '数据')
  // 同步插入表里
  const resp = await mCloudDBZone.executeUpsert(bookInfo)
  callback({
      code: 200,
      data: [],
      msg: '提示内容'
  });
};

export { myHandler };
```

3.2 云数据库-更新数据
> 需要注意的是：要先获取表实例以及定位唯一键
```js
const db_demo = require('./db/bd_demo')
// 上面是注册、加载凭证、打开存储区的代码

let myHandler = async function (event, context, callback, logger) {
  // 获取你的参数
  const body = event.body ? JSON.parse(event.body) : event
  
  // 引入表实例
  const bookInfo = new db_demo.db_demo();
  // 定位唯一键，目的找到对应的数据
  bookInfo.setId('主键数据值') // 主键字段，自己在创建数据库的时候需要自定义主键
  // 注册
  const operator = clouddb.CloudDBZoneObjectOperator.build(bookInfo)
  operator.update('字段名称1', 数据) // 更新对应字段数据
  operator.update('字段名称2', 数据) // 更新对应字段数据
  operator.update('字段名称3', 数据) // 更新对应字段数据
  // ....
  // 更新表里数据
  const resp = await mCloudDBZone.executeUpdate(operator)
  callback({
      code: 200,
      data: [],
      msg: '提示内容'
  });
};

export { myHandler };
```
3.2 云数据库-查询数据
> 这里需要注意的是：查询方式有很多，我这边只用到很基础的。想要更多操作可以看一下官网：https://developer.huawei.com/consumer/cn/doc/AppGallery-connect-Guides/agc-clouddb-otheroperations-servernodejs-0000001569466329#section171182910246
```js
const db_demo = require('./db/bd_demo')
// 上面是注册、加载凭证、打开存储区的代码

let myHandler = async function (event, context, callback, logger) {
  // 获取你的参数
  const body = event.body ? JSON.parse(event.body) : event
  
  // 初始化表
  let cloudDBZoneQuery = clouddb.CloudDBZoneQuery.where(db_demo.db_demo)
  // 查询字段名称等于xxx的数据
  const userCloudDBZoneQuery = cloudDBZoneQuery.equalTo('字段名称', 'xxx');
  const respUser = await mCloudDBZone.executeQuery(userCloudDBZoneQuery);
  const data = respUser.getSnapshotObjects()[0] // 获取对应数据
  callback({
      code: 200,
      data: [],
      msg: '提示内容'
  });
};

export { myHandler };
```
3.3 云数据库-删除数据
> 删除相关的操作我还没使用过，等我使用之后再补充给大家

### 云函数使用云存储
**1. 云存储注册认证**
> 在使用云存储的所有方法时，也是都需要先注册认证哈

```js
import {AGCClient, CredentialParser} from "@agconnect/common-server"
import {StorageManagement, Bucket, File, AGCCloudStorage} from "@agconnect/cloudstorage-server"

// 加载凭证文件
let client_name = "./db/agc-apiclient-chen.json";
let client_path = path.join(__dirname, client_name);
let credential = CredentialParser.toCredential(client_path);
AGCClient.initialize(credential, "clientDW");

let storage = AGCCloudStorage.getInstance("clientDW");
// name: 存储实例名称，获取方式从开发者平台获取
let bucket = storage.bucket('xxxx');

let myHandler = async function (event, context, callback, logger) {
  // xxxx
};

export { myHandler };
```
1.1 获取存储实例名称

![image.png](https://raw.gitcode.com/mc_chenyang/gitcode_knowledge/attachment/uploads/b6835d6a-49ac-4603-a46a-a824c4a27f6d/image.png 'image.png')

**2. 使用云存储的方法**

2.1 上传文件
> 需要注意的是：上传文件需要注意destination的文件路径规则。需要上传的文件对应的路径则是本地路径，文件在云函数的根目录下，。后续我也会将怎么通过端侧上传图片到云函数再上传到云存储的相关操作。

```js
// 以上是认证的相关代码

let myHandler = async function (event, context, callback, logger) {
    const options = {
      destination:  'demo/demo.png', // 对应云存储中的文件路径，demo文件夹需要自己在开发者平台创建，文件名称自定义，存在则覆盖不存在则新增
      onUploadProgress: (event) => {}
    };
    bucket.upload('需要上传的文件路径，例如：./static/demo.png', options);
};

export { myHandler };
```

2.2 下载文件

```js
// 以上是认证的相关代码

let myHandler = async function (event, context, callback, logger) {
    const remoteFile = bucket.file(‘云存储下的文件具体路径’);
    const localFile = '需要上传的文件路径，例如：./static/demo.png'
    const directory = path.dirname(localFile);
    // 判断本地是否有当前文件，有则创建
    if (!fs.existsSync(directory)) {
      fs.mkdirSync(directory, { recursive: true });
    }
    
    // 下载云存储的文件内容到对应的本地文件
    await remoteFile.createReadStream()
      .on('error', (err) => {})
      .on('end', () => {
        // 业务代码
      })
      .pipe(fs.createWriteStream(localFile));
};

export { myHandler };
```

## 总结

这次的鸿蒙端云一体化的开发经验就讲解到这里，可能我讲的不是很细，因为也是第一次使用，希望可以帮助大家。如果上面描述的有问题，欢迎大家在评论区指出来，谢谢。后面我们也会分享端侧的开发经验，以及会将对应app上线，到时候有相关技术讨论的都可以找我们