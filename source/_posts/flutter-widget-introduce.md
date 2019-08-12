---
title: Flutter学习：widget介绍
date: 2019-08-12 16:59:05
tags:
  - flutter
  - widget
categories:
  - Flutter
---

Flutter 从 React 中吸取灵感(组件化思维)，通过现代化框架创建出精美的组件。它的核心思想是用 widget 来构建你的 UI 界面。Widget 描述了在当前的配置和状态下，视图所应该呈现的样子。当 widget 的状态改变时，它会重新构建其描述（展示的 UI），框架则会对比前后变化的不同，以确定底层渲染树从一个状态转换到下一个状态所需的最小更改。

Flutter 的所有类都可以看做是 Widget，大部分的类都是继承自 Widget 类。所以学习和了解 Flutter 有哪些 Widget、怎么分类的，对我们后续快速学习非常有用，也可以对 Flutter 的结构层级有一个大致的了解。

<!-- more -->

### Flutter 的整体结构层级 

{% img /images/2019-08-12/flutter_study_01.png %}
[Flutter架构图  （来源：flutter.dev）](https://flutter.dev/docs/resources/technical-overview)

上图可以看出，Flutter主要分为底层 Engine、Embedder 和 Framework层。Framework 层级是我们最常接触和调用api的层级，负责ui相关的事情、动画、widget、绘图、手势、渲染库等等。


### widget 分类

#### 风格
Widget按风格来说，主要包含 **Material** 和 **Cupertino**
-  Material ： Android 平台的风格的 Material Widget 
-  Cupertino ：iOS 平台风格的扁平化 Widget

#### 状态
Widget按状态来说，分为 **有状态** 和 **无状态** Widget：
- StatelessWidget 
- StatefullWidget
{% img /images/2019-08-12/flutter_study_02.png %}
[Flutter widgets分类 （来源：flutter.dev）](https://flutter.dev/docs/resources/technical-overview)
无状态 Widget 主要是那些无需更新页面显示状态的、无可变状态维护功能的 Widget，如 Text、Icon 等 Widget，只负责显示。而有状态 Widget 主要是组件可以自己维护状态、更新渲染内容，如 Image、Form、TextField 等等。

[<font color='blue'>详细Widget目录分类</font>](https://flutter.dev/docs/development/ui/widgets)

**总结来说，主要包含以下几种分类：**

- 基础Widgets：
  
  Container、Row、Column、Image、Text、Icon、RaisedButton、Scaffold、Appbar、FlutterLogo、Placeholder

- Text 文本显示类：

  Text、RichText、DefaultTextStyle

- Assets、图片、Icons 类：

  Image、Icon、RawImage、AssetBundle

- Input 输入类：

  Form、FormField、RawKeyboardListener

- 动画和 Motion 类

  AnimatedContainer、AnimatedCrossFade、Hero、AnimatedBuilder、DecoratedBoxTransition、FadeTransition、PositionedTransition、RotationTransition、ScaleTransition、SizeTransition、SlideTransition、AnimatedDefaultTextStyle、AnimatedListState、AnimatedModalBarrier、AnimatedOpacity、AnimatedPhysicalModel、AnimatedPositioned、AnimatedSize、AnimatedWidget、AnimatedWidgetBaseState

- 交互模型类：
 - 触摸交互

    Draggable、LongPressDraggable、GestureDetector、DragTarget、Dismissible、IgnorePointer、AbsorbPointer、Scrollable

  - 路由导航

    Route、Navigator

- 样式类：

  Padding、Theme、MediaQuery

- 绘制和效果类：

  Transform、Opacity、DecoratedBox、FractionalTranslation、RotatedBox、ClipOval、ClipPath、ClipRect、CustomPaint、BackdropFilter

- Async 异步模型类：
  FutureBuilder、StreamBuilder

- 滚动类：
  GridView、ListView、NestedScrollView、SingleChildScrollView、Scrollable、Scrollbar、CustomScrollView、NotificationListener、ScrollConfiguration、RefreshIndicator、PageView


- 辅助功能类：

  Semantics、MergeSemantics、ExcludeSemantics

- **Material Components：**
  - App 结构和导航类

    Scaffold、Appbar、BottomNavigationBar、TabBar、TabBarView、MaterialApp、WidgetsApp、Drawer、SliverAppBar

  - 按钮类

    RaisedButton、FloatingActionButton、FlatButton、IconButton、DropdownButton、PopupMenuButton、ButtonBar

  - 输入和选择类

    TextField、Checkbox、Raido、Switch、Slider、Date&Time Pickers

  - 对话框和控制面板类

    SimpleDialog、AlertDialog、BottomSheet、ExpansionPanel、SnackBar）；信息显示类（Image、Icon、Chip、Tooltip、DataTable、Card、LinearProgressIndicator、CircularProgressIndicator、GridView

  - 布局类

    ListTile、Stepper、Divider

- **Cupertino （iOS-style widgets）：**

  CupertinoActionSheet、CupertinoActivityIndicator、CupertinoAlertDialog、CupertinoButton、CupertinoDatePicker、CupertinoDialog、CupertinoDialogAction、CupertinoFullscreenDialogTransition、CupertinoPageScaffold、CupertinoPageTransition、CupertinoPicker、CupertinoPopupSurface、CupertinoSegmentedControl、CupertinoSlider、CupertinoSwitch、CupertinoNavigationBar、CupertinoTabBar、CupertinoTabScaffold、CupertinoTabView、CupertinoTextField、CupertinoTimerPicker

- **布局类：**
 
 - 单个子元素的布局 Widget

    Container、Padding、Center、Align、FittedBox、AspectRatio、ConstrainedBox、Baseline、FractionallySizedBox、IntrinsicHeight、IntrinsicWidth、LimitedBox、Offstage、OverflowBox、SizedBox、SizedOverflowBox、Transform、CustomSingleChildLayout

  - 多个子元素的布局 Widget

    Row、Column、Stack、IndexedStack、GridView、Flow、Table、Wrap、ListBody、CustomMultiChildLayout、LayoutBuilder、ListView、Expanded

接下来，会详细学习了解每个分类下的widget。这是基本的ui构成，需要好好学习。

--- 
参考文章：
-- flutter官网
-- Flutter—从入门到实践(by Ray)