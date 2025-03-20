大家好，我是陈杨。又隔了好久没写文章了，一直都在忙（其实是借口），没有及时跟大家去分享一些技术相关的东西，今天来是跟大家分享两件事。

## 发布会总结

第一件就是：前段时间我去参加了"[原生鸿蒙之夜暨华为全场景新品发布会](https://consumer.huawei.com/cn/press/events/2024/harmonyos-next-and-huawei-all-scenario-new-product-launch-event/ "原生鸿蒙之夜暨华为全场景新品发布会")"，这次我首次参加产品发布会，在发布会上我看到了鸿蒙5.0 NEXT正式落地，也了解到了很多有意思的功能，简单给大家介绍一下我觉得有意义的（个人意见）：

首先是HarmonyOS NEXT是鸿蒙系统的全新自研版本，彻底去除了Linux内核和安卓开源项目代码，只支持鸿蒙内核和鸿蒙系统应用。这样的革新，旨在全面提升系统的流畅度、能效和安全性。

其实这个我没啥可说的，就是牛逼就完了，我不是这个行业的专家也不去分析具体实现方式，一句话就是"遥遥领先，远远超越"。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/5c0e708d3ee647daa5b4b7fdb189940e~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=2rtKHCUlqYcqtYRIk8YMVgYjeJg%3D)

第二个就是隐私安全，以前真的没去仔细了解这方面，都不知道一个授权功能，APP就可以拿到你所有的内容，这点着实可怕。在发布会上了解到原来隐私这么不安全，就像"国王的新衣"一样。这次鸿蒙给我们带来就是不一样的隐私安全，你的数据你做主，给谁看，谁才能看。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/e789993678bf4f35aed77290f6581e1f~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=mX6QtIiCaRZ9y5cKqFbj%2FTkDurk%3D)

第三个就是AI语音修复，我认为一家技术科研公司，就应该需要研发一些对社会有价值，或者带领一群人去探索科技如何改变世界。而不是简简单单升级个照相机，加点电池容量跟快充速度就完了。而这次鸿蒙他做了，基于人工智能修复的语音功能，实现了言语障碍患者的修复成功率已超过80%，让更多的无障碍群体能够正常与人交流。在现场我觉得都很震撼（第一次参加，什么都感觉到震撼，哈哈哈哈）

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/645f32a2cfb443a197e5edbc9ee95d45~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=wTf5FSyA%2F%2F1O38E%2FOrPtV54weEs%3D)

好了，分享完第一件事了；这是我人生第一次参加发布会，挺有意思的，现场听确实比较震撼，希望以后有机会多去参加几次吧（得去舔工作人员了）。

## 莓创图表

第二件事呢，就是我们的莓创技术团队（其实目前就我一个人在维护，有没有想一起用爱发电的，可以联系我）从零到一打造鸿蒙NEXT原生组件：莓创图表2.8版本来了。历经两个月，我们重新调整组件的框架设计，不再是以前的单一设计模式。可以看看我们现在整体一个设计图

![组件库的架构图.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/dab0617bf1f3460d84c9ed536bea7bc1~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=uouJPZTfTm%2BoBEhgHqMo4n%2FN4D4%3D)

### 基础类

我这次把很多功能细化了一下，比如基础类里面就会把图例、轴线、提示层、线条、柱子等等都拆开，可以灵活调用，而且拓展性也很强。可以看一下具体代码：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/10625c5274e045e7a55e8dd34bc4fadc~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=V8yWGDE6qJZ0sTXNuhKxdeqkx5k%3D)

### 动画类

在动画类里面我也是分了很多，有文字的、有线条的、有柱子的等等，这些都是不一样的动画效果。这次我们也是加入贝塞尔曲线来实现不同的动画效果，这个内容很多，后面我可以仔细讲讲（又欠了一篇）。

### 渲染类

这次只要讲一下渲染类这块的历程，花了很多时间，也跟华为提工单了很多问题。首先我们会将每个点都按照30帧去生成动画，比如文字、线条等等，这些都是按照帧去生成对应的动画，然后存放起来。最后通过渲染类来将内容逐帧渲染到画板上，后续我会跟动画类一起讲（又欠了一篇）。

### 图表

这次不光光从技术架构上更新，我们还适配了很多场景与新图表。比如：`Y轴设置最大值与最小值`、`双Y轴`、`图例支持点击控制显示跟隐藏`、`折线与柱状图组合图`、`图例的排列组合`、`多组饼图`、`仪表盘`、`圆形雷达图`等等。支持的案例已经高达`70多种`，欢迎大家使用，也欢迎大家来提出你们需求。

#### 折线图

折线图在此次版本中新增了多条Y轴的展示以及优化了动画、图例可控制显示隐藏等，开箱即用的案例目前已有十几种

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/67a1073527734b1e98d4ed41940daa67~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=qWGvbMItbP%2BTg3vabqYgVWPk%2BmM%3D)

简单展示一下示例代码（写法很echarts）

```js
import { McLineChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct Index {
  @State yAxisMinMaxDefOption: Options = new Options({
    xAxis:{
      data: ['周一','周二','周三','周四','周五','周六','周日'] // 数据
    },
    yAxis: {
      name: '单位/摄氏度',
      min: 10,
      max: 15
    },
    series: [
      {
        name: '最高气温',
        data: [11, 11, 15, 13, 12, 13, 10]
      }
    ]
  })
  build() {
    Row() {
      McLineChart({
        options: this.yAxisMinMaxDefOption
      })
    }
    .height('50%')
  }
}
```

#### 柱状图

柱状图在此次版本中新增了多条Y轴的展示以及渐变模式等多种场景，案例目前已有十几种

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/a75d9a5643034c6d9b6779bdf4e9f23f~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=OQUhC%2Fk5SwgkjXdNTNRJ2FViKCg%3D)

也是简单举个例子

```js
import { McBarChart, Options, chartInterface } from '@mcui/mccharts'

@Entry
@Component
struct Index {
  @State seriesGradientOption: Options = new Options({
    title: {
      show: true,
      text: '渐变柱状图',
      right: 20,
      top: 30
    },
    xAxis: {
      data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日'] // 数据
    },
    series: [
      {
        name: '白天气温',
        gradient: {
          color: ['#53e075', '#7953e075']
        },
        data: [30, 31, 35, 31, 28, 31, 31] // 数据
      },
      {
        name: '夜间气温',
        gradient: {
          color: ['#fa6262', '#83fa6262']
        },
        data: [11, 11, 15, 13, 12, 13, 10] // 数据
      }
    ]
  })

  build() {
    Column() {
      Row() {
        McBarChart({
          options: this.seriesGradientOption
        })
      }
      .height('50%')
    }
  }
}
```

#### 组合图

组合图是此次版本新增的图表，是本次更新中你们最期待的组件，经常收到相关的咨询，这次他来了。组合图融合了折线图跟柱状图的所有属性，案例高达二十几种

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/8bef4e3623c04247b9b32600fd3242a9~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=Vme1NdrdekBXykM34%2FRR4AFKMuU%3D)

简单代码示例

```js
import { McLineBarChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct Index {
  @State yAxisDefOption: Options = new Options({
    title: {
      show: true,
      text: '双Y轴',
      right: 20,
      top: 22
    },
    xAxis:{
      data: ['周一','周二','周三','周四','周五','周六','周日'] // 数据
    },
    yAxis: [
      {
        name: '销售额',
       
      },
      {
        name: '人流量',
        position: 'right'
      }
    ],
    series: [
      {
        name: '人流量',
        data: [1000, 1200, 900, 1500, 900, 1200, 1000],
        type: 'line',
        yAxisIndex: 1
      },
      {
        name: '销售额',
        data: [1500, 1700, 1400, 2000, 1400, 1700, 1500],
        type: 'bar'
      }
    ]
  })
  build() {
    Row() {
      McLineBarChart({
        options: this.yAxisOption
      })
    }
    .height('50%')
  }
}
```

#### 横向条形图

横向柱状图在此次版本中新增了圆角模式以及渐变模式等多种场景，案例目前也高达十几种

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/c5ad73b33301425a90180d18fda115a3~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=fS9i0%2FQ%2BjE421giemT7mguhzyzc%3D)

简单代码示例

```js
import { McHorBarChart, Options, chartInterface } from '@mcui/mccharts'

@Entry
@Component
struct Index {
  @State seriesRadiusOption: Options = new Options({
    title: {
      show: true,
      text: '圆角模式',
      right: 20,
      top: 30
    },
    xAxis:{
      data: ['周一','周二','周三','周四','周五','周六','周日'] // 数据
    },
    series:[
      {
        name:'最高气温',
        color: '#53e075', // 自定义颜色
        barStyle: { // 柱子的样式配置
          borderRadius: [0, 4, 4]
        },
        gradient: {
          color: ['#53e075', '#7953e075']
        },
        data: [30, 31, 35, 31, 28, 31, 31] // 数据
      }
    ]
  })
  build() {
    Row() {
      McHorBarChart({
        options: this.seriesRadiusOption
      })
    }
    .height('50%')
  }
}
```

#### 饼图

饼图在此次更新中新增了玫瑰图与多饼图组合模式等场景、以及优化了动画效果，案例已到达6个以上

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/32ad0f3f64eb450ea5f919c0d9362cd6~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=IuKY2n4Zh%2F2Bbc6TpXk7N3STFp8%3D)

简单的示例

```js
import { McPieChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct Index {
  @State roseTypeOption: Options = new Options({
    title: {
      show: true,
      text: '玫瑰饼状图',
      left: 40,
      top: 20
    },
    series:[
      {
        roseType: true,
        data:[
          {value:435, name:'IOS'},
          {value:310, name:'安卓'},
          {value:234, name:'我心澎湃'},
          {value:135, name:'蓝河'},
          {value:1548, name:'鸿蒙'}
        ]
      }
    ]
  })
  build() {
    Row() {
      McPieChart({
        options: this.roseTypeOption
      })
    }
    .height('50%')
  }
}
```

#### 雷达图

雷达图在此次更新中新增了圆形模式与指标多元化的场景、以及优化了动画效果，案例已到达6个以上

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/b660a4e23bc44de59547e121d9403965~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=XeU32WT1oaRBJAhg%2Fq3lLZKRttc%3D)

简单的示例

```js
import { McRadarChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct Index {
   @State polygonOption: Options = new Options({
    title: {
      show: true,
      text: '圆形雷达',
      right: 20,
      top: 30
    },
    radar: {
      polygon: false,
      indicator: [
        { name: '鸿蒙' },
        { name: 'ios'},
        { name: '安卓'},
        { name: 'Magic' },
        { name: '我心澎湃' },
        { name: '蓝河' }
      ]
    },
    series: [
      {
        
        data: [1000, 800, 600, 700, 266, 132]
      }
    ]
  })
  build() {
    Row() {
      McRadarChart({
        options: this.legendOption
      })
    }
    .height('50%')
  }
}
```

#### 仪表盘

这次我们还开发了仪表盘新图表，除了支持常规的仪表盘场景，还增加环形模式以及多种环形的场景，案例也是拥有6个以上

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/ec825c42a9284fb392349a538e267f2a~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=k25Or0COpVOMMWvIRS0V1CsaIlo%3D)

简单示例代码

```js
import { McGaugeChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct Index {
    @State defOption: Options = new Options({
      series: [
        {
          data: [ { name: '鸿蒙', value: 90 } ]
        }
      ]
    })
    build() {
        Row() {
          McGaugeChart({
            options: this.defOption
          })
        }
        .height('50%')
    }
}
```

## 总结

这次组件重构加开发花了我一个多月，都是一个人在默默开发。但是看到鸿蒙的崛起有我付出的一份力，我就觉得很值（不要说了，用爱发电的人最帅，哈哈哈哈）谢谢大家的支持，如果你觉得可以请用你们的发财小手给我的组件点点赞。十分感谢！！！

本次更新的B站宣传视频地址：<https://www.bilibili.com/video/BV1X9DAYcE6P/>

官网地址：<http://meichuangit.net.cn/>

openHarmony地址：<https://ohpm.openharmony.cn/#/cn/detail/@mcui%2Fmccharts>

github地址：<https://github.com/Yuan-Mr/mcCharts>

gitee地址：<https://gitee.com/cyaofeng_admin/mc-charts>

## 最后

还有一群人需要感谢的，他们就是莓创图表的使用者，谢谢你们这段时间的反馈跟使用，我们也会一直迭代下去，开发出更多业务场景的图表组件，谢谢你们。

<img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/e66a795458914314b19722b6852c98c5~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743071938&x-orig-sign=7F%2FxB3zMFxTvctm7ErgsCBIyuSw%3D" width="200">

## 商务合作

有需要商务合作的都可以找我们，不管是鸿蒙的定制开发，还是网站定制开发，都可以联系我们。
