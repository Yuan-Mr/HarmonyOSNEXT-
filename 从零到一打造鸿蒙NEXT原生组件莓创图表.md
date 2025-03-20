大家好，我是陈杨。又隔了好久没写文章了，一直都在忙（其实是借口），没有及时跟大家去分享一些技术相关的东西，今天来是跟大家分享两件事。

## 发布会总结

第一件就是：前段时间我去参加了"[原生鸿蒙之夜暨华为全场景新品发布会](https://consumer.huawei.com/cn/press/events/2024/harmonyos-next-and-huawei-all-scenario-new-product-launch-event/ "原生鸿蒙之夜暨华为全场景新品发布会")"，这次我首次参加产品发布会，在发布会上我看到了鸿蒙5.0 NEXT正式落地，也了解到了很多有意思的功能，简单给大家介绍一下我觉得有意义的（个人意见）：

首先是HarmonyOS NEXT是鸿蒙系统的全新自研版本，彻底去除了Linux内核和安卓开源项目代码，只支持鸿蒙内核和鸿蒙系统应用。这样的革新，旨在全面提升系统的流畅度、能效和安全性。

其实这个我没啥可说的，就是牛逼就完了，我不是这个行业的专家也不去分析具体实现方式，一句话就是"遥遥领先，远远超越"。

![image](https://github.com/user-attachments/assets/93f85168-ee58-4d05-a6b7-5a61e2df62bc)


第二个就是隐私安全，以前真的没去仔细了解这方面，都不知道一个授权功能，APP就可以拿到你所有的内容，这点着实可怕。在发布会上了解到原来隐私这么不安全，就像"国王的新衣"一样。这次鸿蒙给我们带来就是不一样的隐私安全，你的数据你做主，给谁看，谁才能看。

![image](https://github.com/user-attachments/assets/461720aa-9df1-4d6e-b31c-e0fe1f4ed3f8)


第三个就是AI语音修复，我认为一家技术科研公司，就应该需要研发一些对社会有价值，或者带领一群人去探索科技如何改变世界。而不是简简单单升级个照相机，加点电池容量跟快充速度就完了。而这次鸿蒙他做了，基于人工智能修复的语音功能，实现了言语障碍患者的修复成功率已超过80%，让更多的无障碍群体能够正常与人交流。在现场我觉得都很震撼（第一次参加，什么都感觉到震撼，哈哈哈哈）

![image](https://github.com/user-attachments/assets/f7caa661-2ce4-4bce-bf1b-788bbb0cd691)


好了，分享完第一件事了；这是我人生第一次参加发布会，挺有意思的，现场听确实比较震撼，希望以后有机会多去参加几次吧（得去舔工作人员了）。

## 莓创图表

第二件事呢，就是我们的莓创技术团队（其实目前就我一个人在维护，有没有想一起用爱发电的，可以联系我）从零到一打造鸿蒙NEXT原生组件：莓创图表2.8版本来了。历经两个月，我们重新调整组件的框架设计，不再是以前的单一设计模式。可以看看我们现在整体一个设计图

![image](https://github.com/user-attachments/assets/5b8d19a4-fe90-4e1e-ada4-613ea8ec6f93)


### 基础类

我这次把很多功能细化了一下，比如基础类里面就会把图例、轴线、提示层、线条、柱子等等都拆开，可以灵活调用，而且拓展性也很强。可以看一下具体代码：

![image](https://github.com/user-attachments/assets/91c3e5f4-a1f1-4bdd-b28f-fde553dc9aec)


### 动画类

在动画类里面我也是分了很多，有文字的、有线条的、有柱子的等等，这些都是不一样的动画效果。这次我们也是加入贝塞尔曲线来实现不同的动画效果，这个内容很多，后面我可以仔细讲讲（又欠了一篇）。

### 渲染类

这次只要讲一下渲染类这块的历程，花了很多时间，也跟华为提工单了很多问题。首先我们会将每个点都按照30帧去生成动画，比如文字、线条等等，这些都是按照帧去生成对应的动画，然后存放起来。最后通过渲染类来将内容逐帧渲染到画板上，后续我会跟动画类一起讲（又欠了一篇）。

### 图表

这次不光光从技术架构上更新，我们还适配了很多场景与新图表。比如：`Y轴设置最大值与最小值`、`双Y轴`、`图例支持点击控制显示跟隐藏`、`折线与柱状图组合图`、`图例的排列组合`、`多组饼图`、`仪表盘`、`圆形雷达图`等等。支持的案例已经高达`70多种`，欢迎大家使用，也欢迎大家来提出你们需求。

#### 折线图

折线图在此次版本中新增了多条Y轴的展示以及优化了动画、图例可控制显示隐藏等，开箱即用的案例目前已有十几种

![image](https://github.com/user-attachments/assets/c55c231e-d1e4-4b3e-b61a-d64719091afc)


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

![image](https://github.com/user-attachments/assets/3e420e3c-a1de-46ba-9a90-5b1979f28e38)


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

![image](https://github.com/user-attachments/assets/24a59bb1-aebe-4627-8eb4-c7dd70772d82)


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

![image](https://github.com/user-attachments/assets/99b0adaa-1478-43d9-a901-edcd8afb30f1)


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

![image](https://github.com/user-attachments/assets/84cd9711-c174-456d-97c4-b1a24ee08320)


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

![image](https://github.com/user-attachments/assets/5a352b21-8f5e-47bd-97dd-c3fec290165e)


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

![image](https://github.com/user-attachments/assets/4a6fb391-16a2-4c03-a14a-e125ea3f4326)


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


## 商务合作

有需要商务合作的都可以找我们，不管是鸿蒙的定制开发，还是网站定制开发，都可以联系我们。
