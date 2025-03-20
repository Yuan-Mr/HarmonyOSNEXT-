
大家好，欢迎来到莓创IT技术分享频道，我是陈杨。由于经常有小伙伴一直给我反馈说`莓创图表（mccharts）`数据多的时候经常卡顿，很无奈之前做动画的时候没考虑ArkTs的性能瓶颈，导致现在又要重构开发。于是我重新翻阅文档，看看有没有新的方式来解决。在翻阅官网文档的时候，无意间看到这句话。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/2b3a6b39feed4ad6ad2b031983360a02~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743072031&x-orig-sign=5hPZ1HZ9JczFE5ZkZrkxrlBkjbg%3D)

发现了`@ohos.graphics.drawing`模块性能竟然比canvas好，所以最近我有时间都在学习这个API的功能，准备将莓创图表（mccharts）进行性能升级，打造更加完美的图表组件库。这次我也给大家好好分享一下这个API，给大家准备了三篇，写的不好请见谅。

## 一、导入模块（@ohos.graphics.drawing）

要使用这个绘图模块，第一步得把它导入到咱们的代码里。在代码里加上这行：

```javascript
import { drawing } from '@kit.ArkGraphics2D';
```

这样，咱们就可以使用模块里的各种功能啦。

## 二、绘制基本图形

### （一）绘制矩形

在鸿蒙绘图里，绘制矩形有两种常用方法，都在`Canvas`类里。
第一种方法是`drawRect(rect: common2D.Rect): void`。这里的`rect`参数是一个`common2D.Rect`类型的对象，它就像是矩形的“定位器”和“尺码表”，通过设置`left`（左边的位置）、`right`（右边的位置）、`top`（上边的位置）和`bottom`（下边的位置），能精确确定矩形在画布上的位置和大小。来看个例子：

```javascript
import { common2D, drawing } from '@kit.ArkGraphics2D';
class DrawingRenderNode {
    draw(context) {
        const canvas = context.canvas;
        const rect = { left: 50, right: 150, top: 50, bottom: 150 };
        canvas.drawRect(rect);
    }
}
```

从API version 12开始，又多了一个更方便的方法`drawRect(left: number, top: number, right: number, bottom: number): void`。这个方法直接传入矩形四个角的坐标就行，代码写起来更简洁，而且性能还更好呢！比如：

```javascript
canvas.drawRect(50, 50, 150, 150);
```

### （二）绘制圆形

绘制圆形就用`drawCircle(x: number, y: number, radius: number): void`这个方法。`x`和`y`代表圆心在画布上的坐标，就像给圆心定个“家”，`radius`就是圆的半径啦，而且半径得是大于0的正数，不然就画不出圆咯。举个例子：

```javascript
canvas.drawCircle(100, 100, 30);
```

这行代码就是在坐标`(100, 100)`的地方画一个半径为`30`的圆。想象一下，就像在画布上找到了一个中心点，然后以这个点为中心，用半径为`30`的“画笔”转一圈，一个圆就画好啦。

## 三、设置颜色和混合模式

### （一）绘制颜色

给图形上色可以用`drawColor`方法，它有两种形式。
第一种`drawColor(color: common2D.Color, blendMode?: BlendMode): void`，这里的`color`是`ARGB`格式的颜色，`ARGB`分别代表透明度（Alpha）、红色（Red）、绿色（Green）、蓝色（Blue）。`blendMode`是颜色混合模式，默认是`SRC_OVER`。比如咱们想画个红色的图形，代码可以这样写：

```javascript
import { common2D, drawing } from '@kit.ArkGraphics2D';
canvas.drawColor({ alpha: 255, red: 255, green: 0, blue: 0 }, drawing.BlendMode.SRC_OVER);
```

从API version 12开始，又有了一个更简单直接的方法`drawColor(alpha: number, red: number, green: number, blue: number, blendMode?: BlendMode): void`。直接把颜色通道的值传进去就行，用起来更方便，性能也更好。像这样：

```javascript
canvas.drawColor(255, 255, 0, 0, drawing.BlendMode.SRC_OVER);
```

### （二）颜色混合模式

颜色混合模式就像是给图形颜色玩“魔法组合”，`BlendMode`枚举里有好多有趣的模式可以选择。比如说`CLEAR`模式，用了它，绘制区域就会变得全透明，就好像图形隐身了一样。看代码：

```javascript
canvas.drawColor({ alpha: 255, red: 255, green: 0, blue: 0 }, drawing.BlendMode.CLEAR);
```

而默认的`SRC_OVER`模式，是在目标像素的上面绘制源像素，而且还会考虑源像素的透明度，这样颜色叠加起来就会有不一样的效果，让图形看起来更有层次感。

## 四、案例：绘制一个多彩的界面

咱们来综合运用这些知识，画一个简单的多彩界面，不过显示比较麻烦。假设要画一个蓝色的矩形，再在上面画一个红色的圆形，代码可以这么写：

```javascript
import { UIContext, NodeController, FrameNode, RenderNode, DrawContext } from '@kit.ArkUI';
import { common2D, drawing } from '@kit.ArkGraphics2D';

// 1. 自定义RenderNode
export class MyRenderNode extends RenderNode {
  async draw(context: DrawContext) {
    const canvas = context.canvas;
        // 绘制蓝色矩形
        const blueRect = { left: 30, right: 130, top: 30, bottom: 130 };
        canvas.drawColor({ alpha: 255, red: 0, green: 0, blue: 255 }, drawing.BlendMode.SRC);
        canvas.drawRect(blueRect);
        // 绘制红色圆形
        canvas.drawColor({ alpha: 255, red: 255, green: 0, blue: 0 }, drawing.BlendMode.SRC);
        canvas.drawCircle(80, 80, 30);
  }
}

// 2. 自定义NodeController
export class MyNodeController extends NodeController {
  private rootNode: FrameNode | null = null;
  myRenderNode = new MyRenderNode();

  makeNode(uiContext: UIContext): FrameNode {
    this.rootNode = new FrameNode(uiContext);
    if (this.rootNode === null) {
      return this.rootNode
    }

    const renderNode = this.rootNode.getRenderNode();
    if (renderNode !== null) {
      this.myRenderNode.backgroundColor = 0xffffffff;
      this.myRenderNode.frame = { x: 0, y: 0, width: 4800, height: 4800 };
      this.myRenderNode.pivot = { x: 0.2, y: 0.8 }
      this.myRenderNode.scale = { x: 1, y: 1 }
      renderNode.appendChild(this.myRenderNode);
      renderNode.clipToFrame = true
    }
    return this.rootNode;
  }
}

@Entry
@Component
struct RenderTest {
  @State message: string = 'hello'
  build() {
    Row() {
      Column() {
        // 4. 将自定义NodeController进行显示
        NodeContainer(new MyNodeController())
          .width('100%')
      }
      .width('100%')
      .height('80%')
    }
    .height('100%')
  }
}
```

在这段代码里，先设置颜色为蓝色，用`SRC`混合模式填充矩形区域，再绘制蓝色矩形。接着设置颜色为红色，同样用`SRC`混合模式，然后绘制红色圆形。运行这段代码，就能看到一个蓝色矩形上面有个红色圆形的界面啦。

今天咱们学习了鸿蒙绘图`@ohos.graphics.drawing`模块的基础用法，包括导入模块、绘制基本图形、设置颜色和混合模式，还做了一个小案例。掌握了这些基础，后面咱们就能继续探索进阶和高阶的绘图技巧，画出更厉害的图形啦！赶紧动手试试吧，要是遇到问题，多调试调试代码，或者去官方文档里找找答案。加油，期待大家画出超酷的图形！

也可以加入我们鸿蒙技术交流群，开发鸿蒙原生应用过程有啥问题都可以在群沟通，互相帮助，一起进步。

![e36ee7c117ea59e45aacd442a2e160f.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/dc8bf9e5b9594d3db18df23af89d72f0~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743072031&x-orig-sign=nWJj6Hi9hr3wr3QCcYSZ%2B6sZ0Ko%3D)

在开发鸿蒙原生应用的时候，需要用到图表组件的也可以尝试使用我们封装的。图表的官网地址：[meichuangit.net.cn/](https://link.juejin.cn/?target=http%3A%2F%2Fmeichuangit.net.cn%2F "http://meichuangit.net.cn/")
