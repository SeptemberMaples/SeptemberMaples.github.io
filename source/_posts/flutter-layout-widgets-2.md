---
title: flutter学习-07：流式布局widgets - Wrap、Flow
date: 2019-08-20 11:59:08
tags:
  - flutter
  - widget
categories:
  - Flutter
---

前面学习[基础布局Widgets](http://www.yangyong.xyz/2019/08/19/flutter-layout-widgets-1/ '查看基础布局Widgets')时遇到一个问题，如下图右侧红框中的显示，表示子元素的宽度超出了`Row`的宽度。 `Column`也是一样的问题。 `Flex`、`Row`、`Column`的子元素不会换行，导致宽高会溢出。
{% img /images/2019-08-20/flutter_study_01.png %}

流式布局可以用在商品标签列表、不规则瀑布流列表、网格布局的使用上。简单地说**流式布局就是可以自动换行的布局**。Flutter的流式布局Widgets有 `Wrap` 和 `Flow`。

### Wrap Widget

在 Flutter 中Wrap 组件是一个可以使子控件**自动换行**的布局组件，默认的内部子控件排列方向是水平的。也就是超过指定宽度大小，就自动换到下一行。

Wrap 也是继承自 MultiChildRenderObjectWidget，内部可以放置多个子控件。

#### Wrap 构造方法
```dart
Wrap({
  Key key,
  // 子控件在主轴上的排列方向
  Axis direction: Axis.horizontal,
  // 子控件在主轴方向上的对齐方式
  WrapAlignment alignment: WrapAlignment.start,
  // 主轴方向上子控件的间距 (左右间距)
  double spacing: 0.0,
  // 子控件在纵轴方向上的对齐方式
  WrapAlignment runAlignment: WrapAlignment.start,
  // 子控件在纵轴方向上的间距 （上下间距）
  double runSpacing: 0.0,
  // 交叉轴上子控件的对齐方式
  WrapCrossAlignment crossAxisAlignment: WrapCrossAlignment.start,
  // 文本方向
  TextDirection textDirection,
  // direction为Vertical时子控件排序顺序
  VerticalDirection verticalDirection: VerticalDirection.down,
  List<Widget> children: const []
})
```
可以看出，Wrap的属性和 ['Row'、'Column'、'Flex'](http://www.yangyong.xyz/2019/08/19/flutter-layout-widgets-1/ '查看基础布局Widgets')的很多属性相同。

**我们关注两个属性：**
- `spacing` 主轴方向上子控件的间距 (左右间距);
- `runSpacing` 子控件在纵轴方向上的间距 （上下间距）

#### Wrap 实例
```dart
// Wrap 布局，子元素自动换行
body: Wrap(
  spacing: 10.0,
  runSpacing: 10.0,
  children: <Widget>[
    Text(
      '礼物',
      style: TextStyle(
        backgroundColor: Colors.greenAccent,
        fontSize: 16
      )
    ),
    Text(
      '下雨天去郊游',
      style: TextStyle(
        backgroundColor: Colors.greenAccent,
        fontSize: 16
      )
    ),
    Text(
      '我爸爸',
      style: TextStyle(
        backgroundColor: Colors.greenAccent,
        fontSize: 16
      )
    ),
  ],
),
```
{% img /images/2019-08-20/flutter_study_03.png %}

上面的示例如果把`Wrap`改为`Row`，显示为：
{% img /images/2019-08-20/flutter_study_02.png %}

---

### Flow Widget

`Flow` 就可以自己**自定义规则**来控制子布局排列。Flow 继承自MultiChildRenderObjectWidget，Flow 性能比较好，绘制也比较灵活，可以定制布局效果。

#### Flow 构造方法
```dart
Flow({
  Key key,
  // 子布局排列配置规则。 必须项
  @required FlowDelegate delegate,
  List<Widget> children: const []
})
```

如上，`delegate`规则我们按需编写即可。使用的时候，我们需要创建一个 Delegate 继承自 `FlowDelegate`。

#### Flow 实例

```dart
class _FlowWidgetSampleState extends State<FlowWidgetSample> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Flow Widget'),
      ),
      body: Flow(
        // 子控件排列绘制规则
        delegate: FlowWidgetDelegate(margin: EdgeInsets.all(10)),
        children: <Widget>[
          Container(
              width: 80.0,
              height: 80.0,
              color: Colors.orange,
            ),
            Container(
              width: 160.0,
              height: 80.0,
              color: Colors.teal,
            ),
            Container(
              width: 80.0,
              height: 80.0,
              color: Colors.red,
            ),
            Container(
              width: 80.0,
              height: 80.0,
              color: Colors.yellow,
            ),
            Container(
              width: 80.0,
              height: 80.0,
              color: Colors.brown,
            ),
            Container(
              width: 80.0,
              height: 80.0,
              color: Colors.purple,
            ),
        ],
      ),
    );
  }
}


// 继承自 FlowDelegate
class FlowWidgetDelegate extends FlowDelegate{
  EdgeInsets margin = EdgeInsets.all(10);
  // 构造方法，传入每个child的间隔
  FlowWidgetDelegate({this.margin});

  // 必须要重写的方法：child的绘制控制代码，可以调整尺寸位置
  @override
  void paintChildren(FlowPaintingContext context) {
    var screenWidth = context.size.width;
    double offsetX = margin.left; //记录横向绘制的x坐标
    double offsetY = margin.top; //记录纵向绘制的y坐标
    // 遍历子控件进行绘制
    for (int i = 0; i < context.childCount; i++) {
      // 如果当前x左边加上子控件宽度小于屏幕宽度则继续绘制,否则换行
      var width = context.getChildSize(i).width + offsetX + margin.right;
      if (width < screenWidth) {
        // 绘制子控件
        context.paintChild(i,
            transform: Matrix4.translationValues(offsetX, offsetY, 0.0));
        offsetX = width + margin.left;
      } else {
        offsetX = margin.left;
        offsetY += context.getChildSize(i).height + margin.top + margin.bottom;
        //绘制子控件
        context.paintChild(i,
            transform: Matrix4.translationValues(offsetX, offsetY, 0.0));
        offsetX += context.getChildSize(i).width + margin.left + margin.right;
      }
    }
  }

  // 必须要重写的方法：是否需要重绘
  @override
  bool shouldRepaint(FlowDelegate oldDelegate) {
    return oldDelegate != this;
  }

  // 可选重写方法：是否需要重新布局
  @override
  bool shouldRelayout(FlowDelegate oldDelegate) {
    return super.shouldRelayout(oldDelegate);
  }

  // 可选重写方法：设置Flow布局的尺寸
  @override
  Size getSize(BoxConstraints constraints) {
    return super.getSize(constraints);
  }

  // 可选重写方法：设置每个child的布局约束条件，会覆盖已有的
  @override
  BoxConstraints getConstraintsForChild(int i, BoxConstraints constraints) {
    return super.getConstraintsForChild(i, constraints);
  }
}
```
{% img /images/2019-08-20/flutter_study_04.png %}

[Flow Widget 详情文档](https://api.flutter.dev/flutter/widgets/Flow-class.html)


---

[相关代码](https://github.com/SeptemberMaples/study-section/tree/flutter_study/flutter_study/lib/flutter_07)


---
参考：

- [flutter官网](https://flutter.dev/)
- 【Flutter—从入门到实践(by Ray)】学习笔记