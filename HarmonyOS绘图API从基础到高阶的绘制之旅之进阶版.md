家人们，还记得上次一起探索的鸿蒙绘图API基础用法吗？上手是不是特别容易！今天，咱们就接着深入，开启进阶版的学习，解锁更多复杂又炫酷的绘图技能，让你的鸿蒙应用界面直接“出圈”！

> 我将结合实际开发场景，丰富绘制路径、圆角矩形、绘制图片、画笔与画刷、裁剪区域设置这几个方面的案例，让示例更具实操性和参考价值。

## 一、复杂图形绘制

### （一）路径绘制

当绘制不规则图形，比如独特的图标、创意的装饰元素时，`Path`类就成了得力助手，它能帮我们创建并操控各种复杂路径。这些路径可以由直线、弧线、贝塞尔曲线等组合而成。

创建路径对象很简单：

```javascript
let path = new drawing.Path();
```

接着设置路径起始点：

```javascript
path.moveTo(10, 10);
```

然后添加线段：

```javascript
path.lineTo(100, 100);
```

绘制弧线也不难：

```javascript
path.arcTo(100, 100, 200, 200, 0, 90);
```

除了弧线，还能使用二阶贝塞尔曲线（`quadTo`）和三阶贝塞尔曲线（`cubicTo`） 。

**案例：绘制一个五角星**
五角星的绘制需要结合直线和贝塞尔曲线，利用`Path`类的相关方法，通过精确计算每个顶点的坐标来实现。

```javascript
let path = new drawing.Path();
// 五角星的半径
const radius = 100;
// 计算五角星顶点坐标的辅助函数
function calculateVertex(angle) {
    return {
        x: Math.sin(angle) * radius + 200,
        y: -Math.cos(angle) * radius + 200
    };
}
// 五角星的内角角度
const angles = [
    0,
    Math.PI * 2 / 5,
    Math.PI * 4 / 5,
    Math.PI * 6 / 5,
    Math.PI * 8 / 5
];
// 移动到第一个顶点
path.moveTo(calculateVertex(angles[0]).x, calculateVertex(angles[0]).y);
for (let i = 1; i < 5; i++) {
    const vertex = calculateVertex(angles[i]);
    path.lineTo(vertex.x, vertex.y);
}
// 闭合路径
path.close();
// 附加画笔并绘制路径
let pen = new drawing.Pen();
pen.setStrokeWidth(4);
pen.setColor({ alpha: 255, red: 255, green: 0, blue: 0 });
canvas.attachPen(pen);
canvas.drawPath(path);
canvas.detachPen();
```

在这个案例中，通过数学计算得到五角星每个顶点的坐标，利用`moveTo`和`lineTo`方法依次连接各个顶点，最后使用`close`方法闭合路径，实现五角星的绘制。

### （二）圆角矩形绘制

在追求界面美观与柔和的设计中，圆角矩形很常用。使用`drawRoundRect`方法可以实现：

```javascript
drawRoundRect(roundRect: RoundRect): void
```

其中，`RoundRect`对象包含了矩形的位置、大小以及圆角半径信息。

**案例：绘制一个卡片式布局**
在很多应用中，卡片式布局很常见，使用圆角矩形可以轻松实现。

```javascript
import { common2D, drawing } from '@kit.ArkGraphics2D';
// 卡片的位置和大小
let rect = { left: 50, top: 50, right: 250, bottom: 150 };
// 圆角半径
let roundRect = new drawing.RoundRect(rect, 15, 15);
// 附加画刷设置背景颜色
let brush = new drawing.Brush();
brush.setColor({ alpha: 255, red: 240, green: 240, blue: 240 });
canvas.attachBrush(brush);
canvas.drawRoundRect(roundRect);
canvas.detachBrush();
// 附加画笔绘制边框
let pen = new drawing.Pen();
pen.setStrokeWidth(2);
pen.setColor({ alpha: 255, red: 128, green: 128, blue: 128 });
canvas.attachPen(pen);
canvas.drawRoundRect(roundRect);
canvas.detachPen();
```

在这个案例中，先定义了卡片的位置、大小和圆角半径，创建`RoundRect`对象。然后分别使用画刷设置背景颜色，使用画笔绘制边框，实现了一个简单的卡片式布局。

## 二、图像绘制

### （一）绘制图片

在应用开发中，在画布上绘制图片很常见。`drawImage`系列方法可以满足需求：

```javascript
drawImage(pixelmap: image.PixelMap, left: number, top: number, samplingOptions?: SamplingOptions): void
```

`pixelmap`是要绘制的图片对象，`left`和`top`指定图片在画布上的位置，`samplingOptions`用于设置采样选项（API version 12+支持）。

从API version 12开始，新增了更灵活的方法。`drawImageRect`可以将图片绘制到指定区域：

```javascript
drawImageRect(pixelmap: image.PixelMap, dstRect: common2D.Rect, samplingOptions?: SamplingOptions): void
```

`drawImageRectWithSrc`则能将图片的指定区域绘制到画布的指定区域：

```javascript
drawImageRectWithSrc(pixelmap: image.PixelMap, srcRect: common2D.Rect, dstRect: common2D.Rect, samplingOptions?: SamplingOptions, constraint?: SrcRectConstraint): void
```

**案例：制作一个图片画廊**
假设我们要制作一个简单的图片画廊，展示三张图片，并且对其中一张图片进行局部放大展示。

```javascript
import { image, drawing, common2D } from '@kit.ArkUI';
// 加载三张图片
Promise.all([
    image.createPixelMap(/* 图片1数据 */),
    image.createPixelMap(/* 图片2数据 */),
    image.createPixelMap(/* 图片3数据 */)
]).then(([pixelMap1, pixelMap2, pixelMap3]) => {
    // 绘制第一张图片
    canvas.drawImage(pixelMap1, 20, 20);
    // 绘制第二张图片
    canvas.drawImage(pixelMap2, 150, 20);
    // 绘制第三张图片，并对其局部放大展示
    const srcRect = { left: 50, top: 50, right: 150, bottom: 150 };
    const dstRect = { left: 280, top: 20, right: 400, bottom: 140 };
    canvas.drawImageRectWithSrc(pixelMap3, srcRect, dstRect);
});
```

在这个案例中，使用`Promise.all`同时加载三张图片，然后分别使用`drawImage`和`drawImageRectWithSrc`方法将图片绘制到画布上，实现了一个简单的图片画廊效果，并且对第三张图片进行了局部放大展示。

## 三、绘制状态与属性设置

### （一）画笔与画刷

在绘制图形时，`Pen`类和`Brush`类能帮我们设置线条和填充属性，让图形更具个性。

`Pen`类可以设置线条颜色、宽度、是否抗锯齿等。

```javascript
let pen = new drawing.Pen();
pen.setStrokeWidth(5);
pen.setColor({ alpha: 255, red: 255, green: 0, blue: 0 });
pen.setAntiAlias(true);
canvas.attachPen(pen);
// 绘制图形
canvas.drawRect(10, 10, 110, 110);
canvas.detachPen();
```

`Brush`类用于设置填充属性，比如填充颜色。

```javascript
let brush = new drawing.Brush();
brush.setColor({ alpha: 255, red: 0, green: 255, blue: 0 });
canvas.attachBrush(brush);
// 绘制图形
canvas.drawCircle(100, 100, 50);
canvas.detachBrush();
```

**案例：绘制一个立体按钮**
通过画笔和画刷的配合，可以绘制出具有立体感的按钮。

```javascript
// 绘制按钮背景
let brush = new drawing.Brush();
brush.setColor({ alpha: 255, red: 100, green: 100, blue: 255 });
canvas.attachBrush(brush);
let rect = { left: 100, top: 100, right: 200, bottom: 150 };
canvas.drawRoundRect(new drawing.RoundRect(rect, 10, 10));
canvas.detachBrush();
// 绘制按钮边框，模拟立体效果
let pen = new drawing.Pen();
pen.setStrokeWidth(2);
// 上边框和左边框颜色较浅
pen.setColor({ alpha: 255, red: 200, green: 200, blue: 255 });
canvas.attachPen(pen);
canvas.drawLine(100, 100, 200, 100);
canvas.drawLine(100, 100, 100, 150);
canvas.detachPen();
// 下边框和右边框颜色较深
pen.setColor({ alpha: 255, red: 50, green: 50, blue: 150 });
canvas.attachPen(pen);
canvas.drawLine(100, 150, 200, 150);
canvas.drawLine(200, 100, 200, 150);
canvas.detachPen();
```

在这个案例中，先使用画刷绘制按钮的背景，然后通过画笔分别设置不同颜色绘制边框，模拟出立体效果。

### （二）裁剪区域设置

有时候，我们只想在画布的特定区域进行绘制，`clipRect`方法可以实现：

```javascript
canvas.clipRect({ left: 50, top: 50, right: 150, bottom: 150 }, drawing.ClipOp.DIFFERENCE, true);
```

第一个参数指定裁剪区域的矩形，第二个参数是裁剪操作类型，`ClipOp.DIFFERENCE`表示取差集，即只保留画布上不在裁剪区域内的部分；第三个参数表示是否反转裁剪区域。

**案例：制作一个图片蒙版效果**
通过设置裁剪区域，可以制作图片蒙版效果。

```javascript
import { image, drawing, common2D } from '@kit.ArkUI';
image.createPixelMap(/* 图片数据 */).then((pixelMap) => {
    // 设置裁剪区域为圆形
    const circleRect = { left: 100, top: 100, right: 200, bottom: 200 };
    const circlePath = new drawing.Path();
    circlePath.addCircle(150, 150, 50);
    canvas.clipPath(circlePath, drawing.ClipOp.INTERSECT);
    // 绘制图片
    canvas.drawImage(pixelMap, 0, 0);
    // 清除裁剪区域
    canvas.restore();
});
```

在这个案例中，先创建一个圆形路径，使用`clipPath`方法设置裁剪区域为圆形，然后绘制图片，这样图片就只会显示在圆形区域内，实现了图片蒙版效果。最后使用`restore`方法清除裁剪区域，以便后续正常绘制。

家人们，到这里，鸿蒙绘图API的进阶内容就学完啦！是不是感觉自己离大神又近了一步？赶紧动手实践，把这些知识运用到实际开发中。要是遇到问题，别担心，去官方文档里找找答案，或者和技术交流群里的小伙伴们一起讨论。下一次，咱们将挑战高阶版，一起探索更高级的绘图技巧，期待与大家共同进步！

![e36ee7c117ea59e45aacd442a2e160f.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/dc8bf9e5b9594d3db18df23af89d72f0~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZmIX-adqA==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjA4NDMyOTc3OTEwODA2MiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743072076&x-orig-sign=1jDev99V%2FNiEFTNjnV7I%2FLTaigg%3D)

在开发鸿蒙原生应用的时候，需要用到图表组件的也可以尝试使用我们封装的。图表的官网地址：[meichuangit.net.cn/](https://link.juejin.cn/?target=http%3A%2F%2Fmeichuangit.net.cn%2F "http://meichuangit.net.cn/")

如果你对案例还有其他修改意见，比如增减特定场景案例、调整代码注释等，欢迎随时提出。
