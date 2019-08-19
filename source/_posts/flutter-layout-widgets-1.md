---
title: Flutter学习-06：基础布局widgets
date: 2019-08-19 11:47:04
tags:
  - flutter
  - widget
categories:
  - Flutter
---

布局指的就是各元素、子元素的排列形式，如水平排列、垂直排列、弹性布局、层叠顺序(z-index)等等。在Android、iOS、前端都有相应的写法。Flutter中的基础布局主要有，`Row` 和 `Column` 用于线性布局（横向或者纵向排列子元素）；`Flex` 和 `Expanded` 用于弹性布局（按照比例分配子元素所占大小空间）；`Stack` 和 `IndexedStack` 用于层叠布局（也可以叫帧布局，也就是布局里的元素有前后层级堆叠在一起的）。

<!-- more -->

前面已经学习过，布局Widgets有单一子元素和多子元素之分，详情可看[Widgets相关介绍](/2019/08/12/flutter-widget-introduce/)

### Row Widget

Row 布局组件用来做**水平横向布局**使用，里面的 children 子元素按照水平方向进行排列。

继承关系为： Object > Diagnosticable > DiagnosticableTree > Widget > RenderObjectWidget > MultiChildRenderObjectWidget > Flex > Row

可以看出 Row 是 Flex 的拓展子类，也是多子元素的一个组件之一（内部可以包含多个子元素）。

Row基本用法示例：
{% img /images/2019-08-19/flutter_study_01.png %}

#### Row的构造方法
```dart
Row({
  Key key,
  // 主轴方向上的对齐方式（Row的主轴是横向轴）
  MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
  // 在主轴方向（Row的主轴是横向轴）占有空间的值，默认是max
  MainAxisSize mainAxisSize = MainAxisSize.max,
  // 在交叉轴方向(Row是纵向轴)的对齐方式，Row的高度等于子元素中最高的子元素高度
  CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
  // 水平方向子元素的排列方向：从左到右排列还是反向
  TextDirection textDirection,
  // 表示纵轴（垂直）的对齐排列方向，默认是VerticalDirection.down，表示从上到下。这个参数一般用于Column组件里
  VerticalDirection verticalDirection = VerticalDirection.down,
  // 字符对齐基线方式
  TextBaseline textBaseline,
  // 子元素集合
  List<Widget> children = const <Widget>[],
})
```

因为`Row`是`Flex Widget`的扩展子类，Flex可以结合前端HTML 中的 Flex 弹性盒子布局来理解。有子元素的水平布局方式(Row 是横向轴为主轴)和垂直布局方式(Row 是纵向轴)。

** MainAxisAlignment（主轴属性：主轴方向上的对齐方式，Row 是横向轴为主轴） **
相当于 CSS3 flex的 `justify-content`。

```dart
enum MainAxisAlignment {
  // 按照主轴起点对齐，例如：按照靠近最左侧子元素对齐
  start,

  // 将子元素放置在主轴的末尾，按照末尾对齐
  end,

  // 子元素放置在主轴中心对齐
  center,

  // 将主轴方向上的空白区域均分，使得子元素之间的空白区域相等，首尾子元素都靠近首尾，没有间隙。有点类似于两端对齐
  spaceBetween,

  // 将主轴方向上的空白区域均分，使得子元素之间的空白区域相等，但是首尾子元素的空白区域为1/2
  spaceAround,

  // 将主轴方向上的空白区域均分，使得子元素之间的空白区域相等，包括首尾子元素
  spaceEvenly,
}
```

** CrossAxisAlignment（交叉属性：在交叉轴方向的对齐方式，Row 是纵向轴。Row 的高度等于子元素中最高的子元素高度） **
相当于 CSS3 flex的 `align-items`。
```dart
enum CrossAxisAlignment {
  // 子元素在交叉轴上起点处展示
  start,

  // 子元素在交叉轴上末尾处展示
  end,

  // 子元素在交叉轴上居中展示
  center,

  // 让子元素填满交叉轴方向
  stretch,

  // 在交叉轴方向，使得子元素按照baseline对齐
  baseline,
}
```

** MainAxisSize（在主轴方向子元素占有空间的方式，Row 的主轴是横向轴。默认是 max） **
```dart
enum MainAxisSize {
  // 根据传入的布局约束条件，最大化主轴方向占用可用空间，也就是尽可能充满可用宽度
  max,

  // 与max相反，是最小化占用主轴方向的可用空间
  min,
}
```

#### Row Widget 实例
```dart
Column(
  children: <Widget>[
    Text('Row默认水平排列', textAlign: TextAlign.left,),
    /// Row默认水平排列
    Row(
      children: <Widget>[
        RaisedButton(
          child: Text('Button'),
          onPressed: (){},
        ),
        RaisedButton(
          child: Text('Button2'),
          color: Colors.greenAccent,
          onPressed: (){},
        ),
        Text('Text Widget')
      ],
    ),
    SizedBox(
      height: 20,
    ),

    /// Row 水平方向居中
    Text('Row 水平方向居中'),
    Row(
      // Row的主轴是横向轴
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        RaisedButton(
          child: Text('Button'),
          onPressed: (){},
        ),
        RaisedButton(
          child: Text('Button2'),
          color: Colors.greenAccent,
          onPressed: (){},
        ),
        Text('Text Widget')
      ],
    ),

    /// Row 水平方向布局-spaceAround
    Text('将主轴方向上的空白区域均分，使得子元素之间的空白区域相等'),
    Row(
      // Row的主轴是横向轴
      mainAxisAlignment: MainAxisAlignment.spaceAround,
      children: <Widget>[
        RaisedButton(
          child: Text('Button'),
          onPressed: (){},
        ),
        RaisedButton(
          child: Text('Button2'),
          color: Colors.greenAccent,
          onPressed: (){},
        ),
        Text('Text Widget')
      ],
    ),

    /// Row 垂直方向的排列
    SizedBox(
      height: 20,
    ),
    Text('Row 交叉属性：在交叉轴方向的对齐方式，Row 是纵向轴, 垂直方向的排列'),
    Text('Row 交叉属性：start'),
    SizedBox(
      height: 20,
    ),
    Row(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: <Widget>[
        // Expanded 响应式布局
        Expanded(
          child: (
            Text('The Row widget does not scroll (and in general it is considered an error to have more children in a Row than will fit in the available room). If you have a line of widgets and want them to be able to scroll if there is insufficient room, consider using a ListView. ')
          )
        ),
        RaisedButton(
          child: Text('button'),
          onPressed: () {},
        )
      ],
    ),
    
  ],
)
```
{% img /images/2019-08-19/flutter_study_02.png %}


---

### Column Widget

Column 是**纵向排列子元素**，可以对比着 Row 学习。

继承关系为： Object > Diagnosticable > DiagnosticableTree > Widget > RenderObjectWidget > MultiChildRenderObjectWidget > Flex > Column

#### Column 构造方法

构造方法是一致的，只不过主轴和交叉轴和 Row 是相反的。

---

### Flex 和 Expanded Widget

Flex 组件是 Row 和 Column 的父类，主要用于**弹性布局**，类似于HTML 中的 Flex 弹性盒子布局，可以按照一定比例进行分类布局空间。

Flex 继承自 MultiChildRenderObjectWidget，Flex 也是多子元素的一个组件之一（内部可以包含多个子元素）。

Flex 一般和 Expanded 搭配使用，`Expanded `组件从名字就可以看出它的特点，就是让**子元素扩展占用 Flex 的剩余空间**。

#### Flex 构造方法
```dart
Flex({
  Key key,
  // 子元素排列方向：横向还是纵向。必须项
  @required Axis direction, // Axis.vertical / Axis.horizontal
  MainAxisAlignment mainAxisAlignment: MainAxisAlignment.start,
  MainAxisSize mainAxisSize: MainAxisSize.max,
  CrossAxisAlignment crossAxisAlignment: CrossAxisAlignment.center,
  TextDirection textDirection,
  VerticalDirection verticalDirection: VerticalDirection.down,
  TextBaseline textBaseline,
  List<Widget> children: const []
})
```
`Flex`的必填属性 `direction`表示子元素排列方向，有横向（Axis.horizontal）和纵向（Axis.vertical），分别对应子类Row 和 Column。

 Flex 主要是和 Expanded 搭配使用

 #### Expanded 构造方法
```dart
Expanded({
  Key key, 
  // 占用空间比重、权重
  int flex: 1, 
  // 子元素, 必须项
  @required Widget child 
})
```

#### Expanded 实例
``` dart
// Flex
child: Flex(
  direction: Axis.horizontal, // Row
  children: <Widget>[
    Expanded(
      flex: 2,
      child: Container(
        color: Colors.redAccent,
        child: Text(
          'Text1: 2/5'
        ),
      ),
    ),
    Expanded(
      flex: 1,
      child: Container(
        color: Colors.blueAccent,
        child: Text('Text2'),
      )
    ),
    Expanded(
      flex: 2,
      child: Container(
        color: Colors.greenAccent,
        child: Text('Text3: 2/5'),
      ),
    ),
  ],
)
```
{% img /images/2019-08-19/flutter_study_03.png %}

---

### Stack 和 IndexedStack

Stack 和 IndexStack 都是**层叠布局方式**，内部子元素是有层级堆起来的。

Stack 继承自 MultiChildRenderObjectWidget，Stack 也是**多子元素**的一个组件之一（内部可以包含多个子元素）。

而 IndexedStack 继承自 Stack，扩展了 Stack的一些特性。它的作用是显示第 index 个子元素，其他子元素都是不可见的。所以 **IndexedStack 的尺寸永远是跟最大的子元素尺寸一致**。

Stack 的布局行为，是根据子元素是 positioned 还是 non-positioned 来区分的：

- 对于 `positioned` 的子元素，它们的位置会根据所设置的 top、bottom、right 或 left 属性来确定，这几个值都是相对于 Stack 的左上角；
- 对于 `non-positioned` 的子元素，它们会根据 Stack 的 `aligment` 来设置位置。

**Stack 布局的子元素层级堆叠顺序：最先布局绘制的子元素在最底层，越后绘制的越在顶层。**类似于 Web 中的 z-index。

#### Stack 的构造方法
```dart
Stack({
  Key key,
  // 对齐方式，默认是左上角（topStart）
  AlignmentGeometry alignment: AlignmentDirectional.topStart,
  // 对齐方向
  TextDirection textDirection,
  // 定义如何设置无定位子元素尺寸，默认为loose
  StackFit fit: StackFit.loose,
  // 超过的部分子元素处理方式，默认为 clip
  Overflow overflow: Overflow.clip,
  // 子元素
  List<Widget> children: const []
})
```
**`alignment`属性：**
```dart
// 左上角
  static const Alignment topLeft = Alignment(-1.0, -1.0);
  // 主轴顶部对齐，交叉轴居中
  static const Alignment topCenter = Alignment(0.0, -1.0);
  // 主轴顶部对齐，交叉轴偏右
  static const Alignment topRight = Alignment(1.0, -1.0);
  // 主轴居中，交叉轴偏左
  static const Alignment centerLeft = Alignment(-1.0, 0.0);
  // 居中
  static const Alignment center = Alignment(0.0, 0.0);
  // 主轴居中，交叉轴偏右
  static const Alignment centerRight = Alignment(1.0, 0.0);
  // 主轴底部对齐，交叉轴偏左
  static const Alignment bottomLeft = Alignment(-1.0, 1.0);
  // 主轴底部对齐，交叉轴居中
  static const Alignment bottomCenter = Alignment(0.0, 1.0);

  // 主轴底部对齐，交叉轴偏右
  static const Alignment bottomRight = Alignment(1.0, 1.0);
```

**`fit`属性：**
```dart
enum StackFit {
  // 子元素宽松的取值，可以从min到max的尺寸
  loose,
  // 子元素尽可能的占用剩余空间，取max尺寸
  expand,
  // 不改变子元素的约束条件
  passthrough,
}
```

**`overflow` 属性：**
```dart
enum Overflow {
  // 超出部分不会被裁剪，正常显示
  visible,
  // 超出部分会被裁剪
  clip,
}
```

#### IndexedStack 构造方法
```dart
IndexedStack({
  Key key,
  AlignmentGeometry alignment = AlignmentDirectional.topStart,
  TextDirection textDirection,
  StackFit sizing = StackFit.loose,
  // 多了一个索引，索引的这个元素显示，其他元素隐藏
  this.index = 0,
  // 子元素
  List<Widget> children = const <Widget>[],
})
```

#### Stack 和 IndexedStack 实例
```dart
Column(
  children: <Widget>[
    // Stack 层叠布局
    Stack(
      alignment: AlignmentDirectional.topCenter,
      children: <Widget>[
        Container(
          width: 300,
          height: 300,
          color: Colors.grey,
        ),
        Container(
          width: 200,
          height: 200,
          color: Colors.teal,
        ),
        Container(
          width: 100,
          height: 100,
          color: Colors.blue,
        ),
        Text(
          "Stack",
          style: TextStyle(color: Colors.white),
        ),
      ],
    ),

    SizedBox(
      height: 20,
    ),
    // IndexedStack层叠布局
    IndexedStack(
      // 指定显示的子元素序号，其余子元素隐藏
      index: 2,
      children: <Widget>[
        Container(
          width: 300,
          height: 300,
          color: Colors.grey,
        ),
        Container(
          width: 200,
          height: 200,
          color: Colors.teal,
        ),
        Container(
          width: 100,
          height: 100,
          color: Colors.blue,
        ),
        Text(
          "Stack",
          style: TextStyle(color: Colors.white),
        ),
      ],
    )
  ],
)
```
{% img /images/2019-08-19/flutter_study_04.png %}

---

[相关代码](https://github.com/SeptemberMaples/study-section/tree/flutter_study/flutter_study/lib/flutter_06)


---
参考：

- [flutter官网](https://api.flutter.dev/flutter/widgets/Stack-class.html)
- 【Flutter—从入门到实践(by Ray)】学习笔记