
## **简介**

大家好，我是陈杨。今天主要是分享一下McCharts组件库中的折线图实现过程，记录并分享自己的一些开发经验，感兴趣的可以互相学习。

> McCharts组件库是基于鸿蒙ArkTS语法开发，支持API9以上的版本。图表组件已经开源了，大家可以一起参与进来，不管是新手还是大佬都可以，一起互相学习，开发出更多的组件库，丰富ArkTS的生态库。开源地址：https://github.com/Yuan-Mr/mcCharts
>
> 大家可以通过文章地址去看看，觉得不错的记得给开源项目点一个小星星哦。还可以加入交流群，互相学习。回归正题，

## **功能结构**

实现一个公共组件的时候，我都会先分析一下大概的实现结构以及开发思路，方便我们少走一点歪路；也可以使组件更加容易拓展，维护性更强。不过我不会直接讲所有封装后的，我会把功能一个一个拆开来讲。这样大家才能学习到更多一点东西。下面我简单列一下折线图组件的功能结构：

· 公共属性

1. 宽高

2. 上下左右间距

3. 字体大小

4. 字体颜色

5. 数据

· 绘画坐标轴

1. 绘画X轴

1. 绘画轴线

2. 绘画分割线

3. 绘画刻度线

4. 绘画文本标签

2. 绘画Y轴

1. 绘画轴线

2. 绘画分割线

3. 绘画刻度线

4. 绘画文本标签

· 绘画折线区

1. 绘画折线

2. 绘画标点、文本标签

这就是我大概画的一个功能结构了，可是说是比较简单的一些基础功能了，后续还有点击触发呀、动画呀等等功能也会规划进去。这一期我们先实现上面这些基础的功能，后续在慢慢拓展。

## **公共属性**

一个组件肯定会有一些公共的属性作为动态参数，便于组件之间的信息传递，我们分别讲解一下五个公共属性的一些作用：

1. 画布的宽度（cWidth）和高度（cHeight），这个是最基本的。但是我这里控制是非必传，默认值都是100%就可以了。

2. 画布的内部留白间距（cSpace）。主要是用来控制内容区与画布外框的距离，避免绘画的内容被截掉。

3. 字体大小（fontSize）。主要是来控制整个绘画内容的字体大小，全局性，避免每个小功能都需要传字体大小。

4. 字体颜色（color）。与字体大小的功能一致。

5. 图表数据（data）。用来存储图表内容的数组，其中name与value是必传的。

以下是具体的代码：


```js
// 图表数据的特征接口

interface interface_data {

  name: string | number;

  value: string | number;

  [key: string]: any;

}

 

// 图表的特征接口

interface interface_option {

  cWidth?: string | number,

  cHeight?: string | number,

  fontSize?: string | number,

  color?: string,

  cSpace?: number,

  data?: interface_data[]

}

 

// option 默认值

const def_option: interface_option = {

  cWidth: '100%',

  cHeight: '100%',

  fontSize: 10,

  color: '#333',

  cSpace: 20,

  data: []

}

 

@Component

export struct McLineChart {

  private settings: RenderingContextSettings = new RenderingContextSettings(true)

  private context: CanvasRenderingContext2D = new CanvasRenderingContext2D(this.settings)

  @State options: interface_option = {}

  aboutToAppear() {

    this.options = Object.assign({}, def_option, this.options)

  }

  build() {

    Canvas(this.context)

      .width(this.options.cWidth)

      .height(this.options.cHeight)

      .onReady(() => {

        

      })

  }

}
```

 

## **绘画坐标轴**

讲完画布基本的一些属性之后，我们开始绘画图表内容区吧。首先是绘画坐标轴，坐标轴分为X轴跟Y轴，我们要先开始画Y轴，为什么？因为我已经帮大家踩坑了，原因是：y轴上要显示文本标签，如果一开始没有得到文本标签对应的宽度最大值，那么Y轴跟X轴的起点坐标就会有偏差，会导致绘画全部错位。我先给大家看完整的坐标轴的效果

![image](https://github.com/user-attachments/assets/627a6882-edff-4854-ad4f-63600b326e9d)


### **绘画Y轴**

Y轴整体是由轴线、分割线、刻度线、文本标签。四个部分组成的，四个部分都有先后关系，而且包含一定的算法逻辑，我可以简单用一个概念图讲解一下。

![image](https://github.com/user-attachments/assets/96f9a502-3ec6-4c77-9cb9-7b82e650340c)


我用了一个500\*500的矩形作为我们这次的画布，我们可以图上看到Y轴整体包含了文本标签、Y轴线、分割线、刻度线四个部分。了解canvas的都知道，canvas绘画基本都是通过坐标来定位的，而我们Y轴整体的四个部分的起点与结束坐标都是互相有关系，甚至需要把内部间距、分割间距、y轴线高度、文本最大的宽度四个属性计算在内。讲了这么久概念与思路，还没开始讲代码，真的是不好意思。接下来我们一一的讲解：

1、计算得到文本最长宽度（maxNameW ），我们可以从图中看到，不管是y轴线、刻度线、分割线的起点坐标都是需要内容间距、文本标签、文本标签与分割线间隔（可有可无，看你自己）相加计算得到，而为了保持对齐，所以我们需要计算出文本最长宽度。而y轴的文本一般都是数据（data）对应的数值，所以我们需要得到传入数据（data）中的最大值。然后讲最大值分割成五等分（这里是我们固定写死了，后续可以自行改成动态的）。以下就是计算获取最大文本宽度的代码，部分逻辑我也会写在代码上，请仔细看代码哦：

```js
build() {

    Canvas(this.context)

      .width(this.options.cWidth)

      .height(this.options.cHeight)

      .backgroundColor(this.options.backgroundColor)

      .onReady(() => {

        const values: number[] = this.options.data.map((item) => Number(item.value || 0))

        const maxValue = Math.max(...values)

        let maxNameW = 0

        let cSpiltNum = 5 // 分割等分

        let cSpiltVal = maxValue / cSpiltNum // 计算分割间距

        for(var i = 0; i <= this.options.data.length; i++){

          // 用最大值除于分割等分得到每一个文本的间隔值，而每一次遍历用间隔值乘于i就能得到每个刻度对应的数值了，计算得到得知需要保留整数且转成字符串

          const text = (cSpiltVal * i).toFixed(0)

          const textWidth = this.context.measureText(text).width; // 获取文字的长度

          maxNameW = textWidth > maxNameW ? textWidth : maxNameW // 每次进行最大值的匹配

        }

      })

}
```

 

 

2、绘画文本标签，我们可以从图看到文本标签的x坐标只跟内部间距有关系，而且我们从上面代码就已经得到每个刻度的分割间距了，从而可以得到每个文本的y轴。

```js
.onReady(() => {

   ....

   for(var i = 0; i <= this.options.data.length; i++){

     ...

     // 绘画文本标签

     this.context.fillText(text, this.options.cSpace, cSpiltVal * (this.options.data.length - i) + this.options.cSpace , 0);

   }

})
```

 

 

3、绘画刻度线。我们可以从概念图得到，刻度线的起点x坐标算法是：内部间距（cSpace）加最长文本宽度（maxNameW ）加上文本与刻度线的间距（这点我没有特地画出来，可以看自己业务），起点y坐标则跟文本一样，通过分割间距与下角标的关系得到每个刻度的y坐标；而终点x坐标则是刻度线的长度，终点y坐标则跟起点的y坐标一样，我设置默认长度是5，这样就能得到我们的刻度线了。代码如下：

```js
.onReady(() => {

  ....

  const length = this.options.data.length

  for(var i = 0; i <= length; i++){

    ...

  }

  // 上面是获取最长文本宽度的代码

  // 画线的方法

  function drawLine(x, y, X, Y){

    this.context.beginPath();

    this.context.moveTo(x, y);

    this.context.lineTo(X, Y);

    this.context.stroke();

    this.context.closePath();

  }

  for(var i = 0; i <= length; i++){

    const item = this.options.data[i]

    // 绘画文本标签

    ctx.fillText(text, this.options.cSpace,  cSpiltVal * (this.data.length - i) + this.options.cSpace, 0);

    // 内部间距+文本长度

    const scaleX = this.options.cSpace + maxNameW

    // 通过数据最大值算出等分间隔，从而计算出每一个的终点坐标

    const scaleY = cSpiltVal * (length - i) + this.options.cSpace

    // 这里的5就是我设置文本跟刻度线的间隔与刻度线的长度

    drawLine(scaleX, scaleY, scaleX + 5 + 5, scaleY);

  }

})
```

 

 

4、绘画y轴线。接着就是我们的y轴线了，继续分析概览图。从图中我们可以得到：y轴线的起点x坐标的算法是：内部间距（cSpace）加最长文本宽度（maxNameW ）加上文本与刻度线的间距以及刻度线长度，起点y坐标则是内部上间距；而终点x坐标跟起点x坐标一样，终点y坐标算法是：画布高度减去上下两边的内部间距。通过以上计算关系就能绘画出y轴线了。代码如下：

```js
.onReady(() => {

  ...

  // 上面是绘画其他组成部分代码

   const startX = this.options.cSpace + maxNameW + 5 + 5

   const startY = this.options.cSpace

   const endX = startX

   const endY = this.context.height - (this.options.cSpace * 2)

   drawLine(startX, startY, endX, endY); // 绘画y轴

})
```

 

5、绘画分割线。其实从图中可以看出分割线其实就是跟刻度线差不多，起点x坐标算法是：在刻度线起点x坐标基础上加刻度线长度就好了；起点y轴就跟刻度线一样。而终点的x坐标算法：画布宽度减去起点x坐标就好了；终点的y坐标跟起点的y坐标一样。具体代码如下：

```js
.onReady(() => {

  ....

  // 上面是获取最长文本宽度的代码

  for(var i = 0; i <= length; i++){

    const item = this.options.data[i]

    // 绘画文本标签跟刻度

    ...

    // 绘画分割线

    const splitX = scaleX + 5 + 5

    const splitY = scaleY

    drawLine(splitX, splitY, this.context.width - splitX - this.options.cSpace, splitY);

  }

})
```

### **绘画X轴**

绘画完Y轴之后，我们接着绘画X轴，其实X轴跟Y差不多，只是方向不一样而已。具体的算法我就不一一讲了，我画一下概览图，大家一看就知道了。

![image](https://github.com/user-attachments/assets/e1212b94-d3e6-485d-8d2c-ee9dd6eb863a)


大致算法是跟Y轴绘画逻辑一致的，我列几点不一致的讲一下：

1. 最长对象不一样。Y轴最长是文本宽度；而X轴需要获取的最长是文本高度。

2. 间隔分割数不一样。Y轴是自定义的分割数；而X轴分割线是实际数据的长度。

3. 分割间距长度算法不一样。Y轴算法是用数据最大值处于自定义的分割数；而X轴算法是用画布宽度减去（左右两边的内部间隙以及Y轴宽度（文本最长宽度加上刻度线宽度）），再除去数据的长度，得到每个间隔的长度。

除了上面三点需要注意的，其他的无非就是调换一下计算的位置。X轴整体的代码如下：

```js
.onReady(() => {  
  const cSpace = this.options.cSpace  
  // 上面是绘制y轴的代码  
  ....  
  // 绘制x轴  
  // 获取每个分割线的间距:this.context.width - 20为x轴的长度  
  let xSplitSpacing = parseInt(String((this.context.width - cSpace * 2 - maxNameW) / this.options.data.length))  
  let x = 0;  
  for(var i = 0; i <= this.options.data.length; i++){  
    // 绘画分割线  
    x = xSplitSpacing * (i + 1) // 计算每个数值的x坐标值  
    this.drawLine(x + cSpace + maxNameW, this.context.height - cSpace, x + cSpace + maxNameW, cSpace);  
    // 绘制刻度  
    this.drawLine(x + cSpace + maxNameW, this.context.height - cSpace, x + cSpace + maxNameW, this.context.height - cSpace);  
    // 绘制文字刻度标签  
    const text = this.options.data[i].name  
    const textWidth = this.context.measureText(text).width; // 获取文字的长度  
    // 这里文本的x坐标需要减去本身文本宽度的一半，这样才能居中显示， y坐标这是画布高度减去内部间距即可  
    this.context.fillText(text, x + cSpace + maxNameW - textWidth / 2, this.context.height - cSpace, 0);  
  }

this.context.save();  
  this.context.rotate(-Math.PI/2);  
  this.context.restore();  
})
```

## **绘画折线区**\*\*\*\*

绘画完坐标轴之后，就可以来绘画折线区的内容了。也是整个画布重点的部分；折线区也是分为三个部分：绘画折线、绘画标点、绘画文本。那我们就开始一个一个讲吧：

### **绘画折线**\*\*\*\*

老方法，先给大家画个概览图，好理解一点。

![image](https://github.com/user-attachments/assets/f97dc2cd-c561-43f4-b8f3-12062223b92d)


从上面的图可以看出折线直接就是把实际数据的数值转成x跟y坐标，再通过连线连接起来。而每一个转折点的x坐标算法跟x轴的刻度或者文本是一样的，而y坐标是实际数值通过一定算法转成我们需要的高度。x坐标我们已经获取了，只要是攻克我们的y坐标即可。我们可以通过图来观察一下在画布中与实际数据的关系： 

![image](https://github.com/user-attachments/assets/556a110f-b8c1-44a0-8fee-141b9cabc23e)


首先Y轴的高度代表的是实际数据的最大值，这个我们绘画Y轴的时候就得到的结果，那我们则可以算出Y轴高度与实际数据的缩放倍数（scale），而折线的的每个y坐标对应的也是实际数值，需要把实际数值转换成画布中高度，那么就用实际数值乘与刚刚得到的缩放倍数（scale）就能得到转化后的高度了。

 

虽然我们已经得到每个转折点缩放后的高度，但是如果要跟Y轴坐标一一对应的y坐标的画，还需要用画布的高度减去下边内部高度加x轴高度，再减去缩放后的实际高度。这样算出来的才是我们想要的y坐标值，大概算法关系已经知道了，我把最终代码放在下面：

 

```js
.onReady(() => {  
  ...  
  // 上面是绘制x轴跟y轴的代码  
  // 绘画折线  
  const ySacle = (this.context.height - cSpace *2) / maxValue // 计算出y轴与实际最大值的缩放倍数  
  //连线  
  this.context.beginPath();  
  for(var i=0; i< this.options.data.length; i++){  
    const dotVal = String(this.options.data[i].value);  
    const x = xSplitSpacing * (i + 1) + cSpace + maxNameW // 计算每个数值的x坐标值  
    const y = this.context.height - cSpace - parseInt(dotVal * ySacle); // 画布的高度减去下边内部高度加x轴高度，再减去缩放后的实际高度  
    if(i==0){  
      // 第一个作为起点  
      this.context.moveTo( x, y );  
    }else{  
      this.context.lineTo( x, y );  
    }  
  }  
  ctx.stroke();  
})
```

### **绘画标点** **、** **文本标签**\*\*\*\*

画完折线我们基本能得到很多东西，比如折线上每个转折点的x跟y坐标值。这样对我们绘画标点与文本标签就很方便了，其他的我不多，直接上代码：

 

```js
.onReady(() => {  
  ...  
  // 上面是绘制x轴跟y轴的代码  
  // 绘画折线  
  const ySacle = (this.context.height - cSpace *2) / maxValue // 计算出y轴与实际最大值的缩放倍数  
  this.context.beginPath();  
  for(var i=0; i< this.options.data.length; i++){  
    // 绘画折线代码  
    ...  
    // 绘制标点  
    drawArc(x, y);  
    // 绘制文本标签  
    const textWidth = this.context.measureText(dotVal).width; // 获取文字的长度  
    const textHeight = this.context.measureText(dotVal).height; // 获取文字的长度  
    this.context.fillText(dotVal, x - textWidth / 2, y - textHeight / 2); // 文字  
  }  
  
  function drawArc( x, y ){  
    this.context.beginPath();  
    this.context.arc( x, y, 3, 0, Math.PI*2 );  
    this.context.fill();  
    this.context.closePath();  
  }  
  this.context.stroke();  
})
```

最终效果如下:

![image](https://github.com/user-attachments/assets/76d64c59-7169-41b9-9439-248bd0f5510b)


 

## **总结**\*\*\*\*

感谢大家的观看，这次技术分析就到此为止，希望大家能够学习到很多东西。也能自己开发想要开发的效果，后续我们会把chart相关系列的组件封装到组件库发布到市场上，这样可以直接开箱即用了。敬请期待吧，关注我；后续还有很多技术的分享，不要错过！！！！也可以技术交流交流。
