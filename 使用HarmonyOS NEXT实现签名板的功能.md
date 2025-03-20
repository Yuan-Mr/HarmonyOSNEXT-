## 简介

大家好，我是一只会打代码的羊。今天来分享一篇之前使用ArkTS API9版本实现的签名板功能，目前鸿蒙已经推出API12了，对比API9的时候，现在实现一个功能太简单了。这期主要讲一下之前实现功能有多难受，以及如何实现。，现在这套代码也是可以直接迁移到API12版本的。

> 对了，目前我们开发的图表组件已经开源了，有兴趣的伙伴可以参与一下。日常开发项目需要用图表组件的也可以安装立即使用，使用风格与Echarts完全一样，但是效果还在完善，哈哈哈哈。不要嫌弃哈。觉得不错的记得给开源项目点一个小星星哦。还可以加入交流群，互相学习。回归正题，

`官方文档地址`：http://meichuang.org.cn/GettingStarted

`Github地址`：https://github.com/Yuan-Mr/mcCharts

## 工具介绍

这次我要分享的是使用ArkTs中的canvas实现签名板的功能，canvas画布估计大家都很熟悉，我们会用它经常实现一些画板或者图表、表格之类的功能。canvas签名板是我在开发APP过程中实现的一个功能，开发过程中也是遇到比较多的问题。我会按照以下几点来讲解开发整个过程：

### 一：痛点-屏幕旋转

实现签名板的第一个功能就是旋转屏幕。旋转屏幕在各种框架中都有不一样的方式，比如：

· 在H5端，我们一般是使用CSS中的transform属性中的rotate()方法来强制将网页横屏，然后实现一系列功能

· 在嵌套第三方APP中，我们一般是调用对应的SDK提供的方法，即可实现旋转屏幕

· .....

实现方式应该还有很多吧，各有千秋，所以Harmony肯定也会提供对应API方法来设置旋转屏幕。而在Harmony的开发文档里面查找与使用相关方案的结果成为我第一个大痛点。我大概列一下我的经历：

1. 首先我在查阅文档先看到： **@ohos.display** 这个API主要是用来获取屏幕一些相关的设备信息。一开始我还以为可以设置，一般可以获取就可以设置，翻阅到对应的属性方法后，发现还是只能获取不能设置，既然不行那就只能找其他API了。

2. 后面查阅相关博客发现还可以通过**module.json5**中配置字段为"orientation": "landscape"就可以设置横屏了，但是这个是全局的，我是需要针对某个页面的。

3. 最坑的在这个API：@ohos.settings。官网是这样介绍的：“本模块提供访问设置数据项的能力”。那证明他是可以获取或者修改的，我也查到了旋转屏幕的属性，文档上是写着可读可写，但是我引入对应修改方法之后一直报该属性只可读。一开始我以为写错了，查找了很多资料，都没有找到相关案例。后面我又怀疑是我API版本的问题，把7.0跟6.0的版本都装了一下，发现还是不行。我又换一个思路，因为之前一直都是直接预览没有用模拟器；把模拟器需要的依赖都装了一遍，最后运行起来发现还是不行，崩溃了，搞了两天。 

最后我只能在博客寻找大佬的支持了，最终在大佬的提示下完成了这个功能。大佬说：“在页面内通过 Window 对象的 setPreferredOrientation() 方法实现横竖屏切换”。以下是我实现的完整代码：

    // 在EntryAbility.ts中获取窗口实例并赋值给全局变量，如此所有页面都可以通过全局// 变量去修改窗口信息，不需要重新获取
    import UIAbility from '@ohos.app.ability.UIAbility';
    import window from '@ohos.window';
    export default class EntryAbility extends UIAbility {
      onWindowStageCreate(windowStage: window.WindowStage) {
        windowStage.getMainWindow((err, data) => {
          if (err.code) {
            console.error('获取失败' + JSON.stringify(err));
            return;
          }
          console.info('获取主窗口的实例：' + JSON.stringify(data));
          globalThis.windowClass = data // 赋值给全局变量windowClass
        });
      }
    }

<!---->

    // 在具体页面中的使用
    import window from '@ohos.window';
    @Entry
    @Componentstruct SignatureBoard {
      onPageShow() {
    // 获取旋转的方向，具体可以查看对应文档
        let orientation = window.Orientation.LANDSCAPE_INVERTED;
        try {
          // 设置屏幕旋转
          globalThis.windowClass.setPreferredOrientation(orientation, (err) => {});
        } catch (exception) {
          console.error('设置失败: ' + JSON.stringify(exception));
        }
      }
    }

好了，第一个痛点已经解决了。果然不懂就要问，或许在别人那里一下子就能帮你解决问题。还是得感谢大佬们！！！

#### 二：技术点-canvas画布

解决了我们的痛点，那接下来就要实现我们的签名功能了，这个很简单。因为在之前就已经开发过了，只要将对应的语法转成ArkTs的语法就好了。接下来我直接上代码解析：

### **2.1 按照官方文档使用canvas组件**

    @Entry@Component
    struct SignatureBoard {
      private settings: RenderingContextSettings = new RenderingContextSettings(true)
      private context: CanvasRenderingContext2D = new CanvasRenderingContext2D(this.settings)
      build() {
        Column() {
          Canvas(this.context)
            .width('100%')
            .height('100%')
            .backgroundColor('#fff')
            .onReady(() => {
            })
        }
        .width('100%')
        .height('100%')
      }
    }

### **2.2 设置画笔的属性以及绑定手势功能。在js中我们基本都是使用鼠标事件来实现的，而在ArkTs中是通过手势方法来监听手指在屏幕上的操作，有很多种，大家需要用到的可以去查看对应的文档**

    build() {
        Column() {
          Canvas(this.context)
            .width('100%')
            .height('100%')
            .backgroundColor('#fff')
            .onReady(() => {
              this.context.lineWidth = 3; // 设置画笔的粗细
              this.context.strokeStyle = "#000"; // 设置画笔的颜色
              // 还可以设置很多，根据自己业务需要
            })
            .gesture(
              PanGesture(this.panOption)
                .onActionStart((event: any) => {
                   // 手指按下的时候
                })
                .onActionUpdate((event: any) => {
                   // 手指移动的时候
                })
                .onActionEnd(() => {
                   // 手指离开的时候
                })
            )
      }

### **2.3 我们实现的手势的绑定，那么就可以实现手指在屏幕上滑动之后画布就绘画出对应的轨迹路线了，这里将会使用到一些画布的功能。**

    @Entry
    @Componentstruct SignatureBoard {
      private lastX: number = 0;
      private lastY: number = 0;
      private isDown: Boolean = false;
      private panOption: PanGestureOptions = new PanGestureOptions({ direction: PanDirection.All, distance: 1 })
      private settings: RenderingContextSettings = new RenderingContextSettings(true)
      private context: CanvasRenderingContext2D = new CanvasRenderingContext2D(this.settings)
      draw(startX, startY, endX, endY) {
        // 起点
        this.context.moveTo(startX, startY);
        // 终点
        this.context.lineTo(endX, endY);
        // 调用 stroke，即可看到绘制的线条
        this.context.stroke();
      }
      build() {
        Column() {
          Canvas(this.context)
            .width('100%')
            .height('100%')
            .backgroundColor('#fff')
            .onReady(() => {
              this.context.lineWidth = 3;
              this.context.strokeStyle = "#000";
            })
            .gesture(
              PanGesture(this.panOption)
                .onActionStart((event: any) => {
                  this.isDown = true;
                  // 按下时的点作为起点
                  this.lastX = event.localX;
                  this.lastY = event.localY;
                  // 创建一个新的路径
                  this.context.beginPath();
                })
                .onActionUpdate((event: any) => {
                  // 没有按下就不管
                  if (!this.isDown) return;
                  const offsetX = event.localX
                  const offsetY = event.localY
                  // 调用绘制方法
                  this.draw(this.lastX, this.lastY, offsetX, offsetY);
                  // 把当前移动时的坐标作为下一次的绘制路径的起点
                  this.lastX = offsetX;
                  this.lastY = offsetY;
                })
                .onActionEnd(() => {
                  this.isDown = false;
                  // 关闭路径
                  this.context.closePath();
                })
            )
        }
        .width('100%')
        .height('100%')
      }
    }

以上就是我们实现签名板的完整思路以及代码了，有几个需要注意的点是：

1. new PanGestureOptions实例的时候需要把distance设置小一点，值越小灵敏度就越高

2. PanGesture的回调方法中event参数，官方默认给的属性类型为GestureEvent。但是我在编辑器源码中查看该属性没有我们定义我们想要的localX、localY，但是实际是有返回的，如果直接用编辑器会报错。所以需要将event定为any类型，这样就可以获取且不报错了。不过我不知道有没有其他方案哈。懂得可以在评论区告诉我

这次的画布签名板的功能就讲到这里了，希望能够帮助你们，后续我还会分享出更多在项目中经常用到的工具，感谢大家观看。
