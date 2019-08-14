---
title: Flutter学习-04：基础widgets - AppBar、AlertDialog、Icon
date: 2019-08-14 16:21:36
tags:
  - flutter
  - widget
categories:
  - Flutter
---

### AppBar Widget

> AppBar 是页面的顶部标题栏，里面可以定制各种按钮、菜单等，一般配合 `Scaffold` 使用。AppBar 是一个 StatefulWidget。

{% img /images/2019-08-14/flutter_study_01.png '来源：flutter.dev'%}

#### AppBar Widget 构造方法
```dart
AppBar({
  Key key,
  // 标题栏最左侧图标，首页一般默认显示logo，其它页面默认显示返回按钮，可以自定义
  Widget leading,
  // 是否提供控件占位
  bool automaticallyImplyLeading: true,
  // 标题内容
  Widget title,
  // 标题栏上右侧的菜单，可以用IconButton显示，也可以用PopupMenuButton显示为三个点
  List<Widget> actions,
  // AppBar下方的控件，高度和AppBar高度一样，通常在SliverAppBar中使用
  Widget flexibleSpace,
  // 标题栏下方的空间，一般放TabBar
  PreferredSizeWidget bottom,
  // 控制标题栏阴影大小
  double elevation,
  ShapeBorder shape,
  // 标题栏背景色
  Color backgroundColor,
  // 亮度，有白色和黑色两种主题
  Brightness brightness,
  // AppBar上图标的颜色、透明度、和尺寸信息
  IconThemeData iconTheme,
  IconThemeData actionsIconTheme,
  // AppBar上的文字样式
  TextTheme textTheme,
  // 是否进入到状态栏
  bool primary: true,
  // 标题是否居中
  bool centerTitle,
  // 标题间距，如果希望title占用所有可用空间，请将此值设置为0.0
  double titleSpacing: NavigationToolbar.kMiddleSpacing,
  // 透明度
  double toolbarOpacity: 1.0,
  double bottomOpacity: 1.0
})
```

#### AppBar Widget 实例
一个简单的用法：
```dart
// 部分代码
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        // leading
        leading: IconButton(
          icon: Icon(Icons.menu),
          onPressed: () => {},
        ),
        // title
        title: Text('AppBar Widget 学习'),
        // actions
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.access_time),
            padding: EdgeInsets.all(10),
            onPressed: () => {},
          ),
          IconButton(
            icon: Icon(Icons.card_giftcard),
            padding: EdgeInsets.all(10),
            onPressed: () => {},
          ),
        ],
        backgroundColor: Colors.redAccent,
      ),
    );
  }
```
效果为：
{% img /images/2019-08-14/flutter_study_02.png %}

加上 AppBar 的 bottom 部分，实现一个相对复杂的效果。
```dart
Scaffold(
      appBar: AppBar(
        leading: IconButton(
          icon: Icon(Icons.menu),
          onPressed: () {},
        ),
        title: Text('AppBar Widget'),
        actions: <Widget>[
          IconButton(icon: Icon(Icons.share), onPressed: () {}),
          /// 三个点，表示打开更多
          PopupMenuButton(
            itemBuilder: (BuildContext context) => <PopupMenuItem<String>>[
              PopupMenuItem<String>(
                child: Text('个人中心'),
                value: 'profile',
              ),
              PopupMenuItem<String>(
                child: Text('更多列表'),
                value: 'list',
              ),
            ],
          ),
        ],
        /// bottom 标题栏下方的空间，一般放TabBar
        bottom: TabBar(
          controller: _tabController,
          isScrollable: true,
          indicatorColor: Colors.white,
          tabs: <Widget>[
            Tab(
              text: 'tab1',
              icon: Icon(Icons.add),
            ),
            Tab(
              text: 'tab2',
              icon: Icon(Icons.add),
            ),
            Tab(
              text: 'tab3',
              icon: Icon(Icons.add),
            ),
            Tab(
              text: 'tab4',
              icon: Icon(Icons.add),
            ),
            Tab(
              text: 'tab5',
              icon: Icon(Icons.add),
            ),
          ],
        ),

        backgroundColor: Colors.redAccent,
      ),
      body: TabBarView(
        controller: _tabController,
        children: <Widget>[
          Center(
            child: Text('data1'),
          ),
          Center(
            child: Text('data2'),
          ),
          Center(
            child: Text('data3'),
          ),
          Center(
            child: Text('data4'),
          ),
          Center(
            child: Text('data5'),
          ),
        ],
      ),
    );
```
效果为：
{% img /images/2019-08-14/flutter_study_03.png %}