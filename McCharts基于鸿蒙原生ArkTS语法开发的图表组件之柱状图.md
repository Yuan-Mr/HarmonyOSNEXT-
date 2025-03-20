大家好，我是陈杨。在上一篇文章中，我简要介绍了折线图的实现逻辑，并解释了整体图表的绘制规则。根据这些规则，我们还可以绘制更多种类的图表组件。在本期中，我将讲解如何实现柱状图，并引入了一个新的功能。鉴于柱状图跟折线图可共用的基础配置很多，我将不再重复介绍基础知识，如果你对此感兴趣，可以翻阅上一篇文章了解更多内容。

> 在开始技术讲解之前，我想插播一个消息。为了方便大家今后使用与图表相关的组件，我已经陆续封装了相关系列的组件，使其可以开箱即用。未来，我还将采取开源策略，希望大家共同分享创造更多实用的工具。目前，相关组件的使用文档地址为：<http://meichuang.org.cn/McBarChart>

进入正题，老规矩先把实现结构整理出来。结构中有一些我上篇文章已经讲过，所以这里不再做过多的阐述。直讲新内容
- 公共属性
    - 画布的宽高
    - 画布内部间距
    - ...
- 绘画坐标
    - 绘画x与y坐标轴
    - ...
- 绘画柱状区
    - 绘画柱子
    - 绘画特性功能（文本标签）
- **Tooltip（*新*）**
    - 基本属性
    - 定位显示


### 绘画柱状区

在上一篇文章中，已经包含了定义公共属性和绘制坐标的代码。如果您对此感兴趣，可以去查看。本期的内容主要涵盖了柱状图区域的基本属性、柱子的绘制以及特性功能的实现。

#### 绘画柱子

先绘画出柱状区的概览图：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c551614c70ff4570b63e12ffcac14a6f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1206&h=1120&s=111816&e=png&b=ffffff)

通过概览图，我们可以得到以下步骤：首先计算出每个刻度的X坐标，然后将数值转换为对应的高度，并设置柱子的宽度，最后利用canvas的矩形绘制方法来绘制相应的柱子。接下来我们将详细介绍具体的算法：


**每个刻度的X坐标算法**：首先将实际数据长度`length`除以画布的宽度`width`，得到等分刻度。然后，将等分刻度乘以索引值即可得到每个刻度的X坐标。

**数据转化高度算法**：首先将实际数据的最大值`maxValue`除以画布的高度`height`，得到缩放倍数。然后，使用每个刻度的实际数值乘以缩放倍数即可得到对应的高度值。

**柱子宽度**：设置基础值，可以动态传参。

了解大概算法，我们将算法转换成代码。代码如下：


```js
.onReady(() => {  
  ...  
  // 上面是绘制x轴跟y轴的代码  
  // 绘画折线  
  const ySacle = (this.context.height - cSpace *2) / maxValue // 计算出y轴与实际最大值的缩放倍数  
  //连线
  for(var i=0; i< this.options.data.length; i++){  
    const dotVal = String(this.options.data[i].value)  
    const barW = 10
    // 画布的高度减去下边内部高度加x轴高度
    const barH = parseInt(dotVal * ySacle) 
    // 计算每个数值的x坐标值，减去barW/2是为了柱子能够居中显示
    const x = xSplitSpacing * (i + 1) + cSpace + maxNameW - barW / 2 
    // 由于画布的左边是从左上角开始计算的，用画布高度减去缩放后的高度得到柱子顶部的坐标
    const y = this.context.height - cSpace - barH 
    ctx.beginPath()
    ctx.rect( x, y, barW, barH)
    ctx.fillStyle = "green"
    ctx.fill()
    ctx.closePath()
  }  
  ctx.stroke();  
})
```

#### 绘画特性功能（文本标签）

绘制文本标签其实也很简单。由于我们已经计算出每根柱子的起点坐标，所以只需要将计算得到的坐标减去文本的宽度和高度，就可以得到文本标签的位置。以下是具体的代码示例：

```js
.onReady(() => {  
  ...  
  // 上面是绘制x轴跟y轴的代码  
  // 绘画折线  
  const ySacle = (this.context.height - cSpace *2) / maxValue // 计算出y轴与实际最大值的缩放倍数  
  //连线
  for(let i = 0; i < this.options.data.length; i++){  
    const dotVal = String(this.options.data[i].value)  
    const barW = 10
    // 画布的高度减去下边内部高度加x轴高度
    const barH = parseInt(dotVal * ySacle) 
    // 计算每个数值的x坐标值，减去barW/2是为了柱子能够居中显示
    const x = xSplitSpacing * (i + 1) + cSpace + maxNameW - barW / 2 
    // 由于画布的左边是从左上角开始计算的，用画布高度减去缩放后的高度得到柱子顶部的坐标
    const y = this.context.height - cSpace - barH
    ... 绘画柱子
    
    // 绘制文本标签  
    const textWidth = this.context.measureText(dotVal).width; // 获取文字的长度  
    const textHeight = this.context.measureText(dotVal).height; // 获取文字的长度  
    this.context.fillText(dotVal, x - textWidth / 2, y - textHeight / 2); // 文字
  }  
  ctx.stroke();  
})
```

整个绘制基础柱状图的功能已经完成了。大家可以尝试使用，并根据自己的业务需求来实现相应的功能。希望这些代码能够对您有所帮助！

### Tooltip（提示层）

在讲解完整个柱状图的绘画之后，接下来我们将探讨如何实现提示层功能。提示层功能在使用图表呈现数据时是必不可少的，它可以让数据更加直观地展示，同时增加图表的交互性，避免过于单调。

如果使用传统的 JavaScript 开发机制，实现提示层功能相对简单：点击图表内容，判断坐标获取对应索引数据，动态创建 `<div>` 元素来展示数据，计算画布和提示层的宽高，并决定提示层的最佳位置。这是大致的实现思路。

然而，在 ArkTS 语言中，我们需要解决以下两个问题：

1.  无法动态创建 `<div>` 元素。
1.  无法实时获取元素的宽高。
1.  无法触发组件之外的内容隐藏提示层。

针对这些问题，我们可以按照以下步骤将思路转化为代码并解决相应的问题。

#### 绑定事件/创建动态组件

首先，对画布进行单击事件的绑定，并获取点击位置的 `x` 和 `y` 值。然后循环遍历数据，对比判断 `x` 值是否大于对应索引的刻度值，如果大于，则记录对应的索引数据，否则继续判断。代码示例如下：

```js
Canvas(this.context)
  .width('100%')
  .height('100%')
  .onReady(() => {
    // 绘画内容区
  })
  .gesture(
    TapGesture({ count: 1 })
      .onAction((e: GestureEvent) => {
         const ctx = this.context
         // 获取点击的x、y值
         let pos = {
          x: e.localX,
          y: e.localY
        }
        // 获取x轴的刻度等分
        let xSplitSpacing = parseInt(String((ctx.width - cSpace * 2 - maxNameW) / this.options.data.length))
        // 循环数据判断
        const activeObj = {}
        const activeX = null
        for(let i = 0; i < this.options.data.length; i++){  
           const item = this.options.data[i]
           if(pos.x > i * xSplitSpacing) {
               activeObj = item
               activeX = i * xSplitSpacing 
           }
        }
        // 显示提示层
        if(this.activeX !== null) {
           ....
        }
      })
  )
```

由于我们没有办法直接动态添加元素，那我们要先定义好一个组件来呈现我们的数据，动态控制显示跟隐藏。

```js
@State tooltipInfo: InterfaceObj = {}
@State tooltipPos: InterfaceObj = {
    x: -100000,
    y: -100000
}

Column() {
  Canvas(this.context)
  .width('100%')
  .height('100%')
  .onReady(() => {
    // 绘画内容区
  })
  .gesture(
    TapGesture({ count: 1 })
      .onAction((e: GestureEvent) => {
        ...判断逻辑
        // 显示提示层
        if(this.activeX !== null) {
           this.tooltipInfo = activeObj
           this.tooltipPos.x = activeX
        }
      })
  )
 if(Object.keys(this.tooltipInfo).length) {
    Column () {
      Text(this.tooltipInfo.title)
      ForEach(this.tooltipInfo.data, (item, index) => {
        Text(item.name + '：' + item.num)
      })
    }
 }
}

```

好的，利用ArkTS提供的渲染控制能力来动态显示元素节点是一个不错的解决方案。这样我们就成功地解决了无法动态添加节点的问题，并顺利完成了第一步。

#### 定位适配显示

接下来，我们需要实现适配显示的定位功能，使提示层能够定位在鼠标点击的位置，并且不超出屏幕范围。在上面显示提示层时，我记录了点击图表时数据项对应的 X 坐标，这样就可以为提示层设置相对定位的 X 属性。至于 Y 轴的定位，我选择居中显示，当然你们也可以根据数据项的 Y 坐标进行定位。

在设置提示层的 X 坐标时，当点击右边最后几个数据项或者提示层内容较大时，可能会导致提示层超出画布内容，从而造成数据显示不全。解决这个问题的方法也比较简单：判断获取提示层自身的宽度加上 X 坐标是否大于画布宽度，如果大于，则证明超出了画布的范围。然后，将 X 坐标减去画布的宽度，就可以得到最终的 X 坐标。

然而，问题也随之而来。由于 ArkTS 没有提供直接获取某些元素宽度和高度的功能，一开始我以为无法继续下去了。但是，在仔细阅读官方文档之后，终于发现了一点线索。这里我就不卖关子了，这个 API 就是"组件区域变化事件"。你可以在官方文档中找到相关的信息：

[组件区域变化事件，快速跳转](https://developer.harmonyos.com/cn/docs/documentation/doc-references-V3/ts-universal-component-area-change-event-0000001478061665-V3)

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/52826eafb47d4491b1b8c6f02f878eb1~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=2040&h=1368&s=260670&e=png&b=fefefe)

这个事件主要用于监听某个元素位置或尺寸的变化，并在变化发生时回调，提供最新的位置和尺寸信息。这正好符合我们的需求，因为我们的 X 坐标是不断变化的，这样我们就可以获取到元素的尺寸了。下面是完整的代码示例：

```js
@State tooltipInfo: InterfaceObj = {}
@State tooltipPos: InterfaceObj = {
    x: -100000,
    y: -100000
}

Column() {
  Canvas(this.context)
  .width('100%')
  .height('100%')
  .onReady(() => {
    // 绘画内容区
  })
  .gesture(
    // 点击画布相关事项
  )
 if(Object.keys(this.tooltipInfo).length) {
    Column () {
      Text(this.tooltipInfo.title)
      ForEach(this.tooltipInfo.data, (item, index) => {
        Text(item.name + '：' + item.num)
      })
    }
    .position({
      x: this.tooltipPos.x,
      y: this.tooltipPos.y
    })
    .onAreaChange((oldValue: Area, newValue: Area) => {
        const { x } = this.tooltipInfo.pos
        const { width: W, height: H } = this.context
        const { width, height } = newValue
        if (x + 40 + width > W - 10) {
          this.tooltipPos.x = x - width + 20
        } else {
          this.tooltipPos.x = x + 40
        }
        this.tooltipPos.y = H / 2 - height / 2
    })
 }
}

```

### 结束

讲到这里，我们已经完成了柱状图组件以及提示层功能的开发，并成功封装成了组件，即将发布到相关的文档上。希望大家在使用过程中能够学到很多知识。如果你有任何需要交流的地方，请在下面留言评论，我会第一时间回复你。感谢大家的支持！

