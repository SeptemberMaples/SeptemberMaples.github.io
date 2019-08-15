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

---

### Icon Widget

> Icon Widget 主要是用来显示图标相关的操作。Flutter Icon 也可以使用 unicode、矢量图标、iconfont 里的图标、Flutter 自带的 Material 图标（需要在 pubspec.yaml 中配置开启）。

Material 风格图标使用通过 `Icons.` 来调用，如 `Icons.add`，这样就可以使用加号图标了。Flutter 的 Icon 也封装了`ImageIcon`、`IconButton` 来提供使用。

Material Design 所有图标可以在官网查看：https://material.io/tools/icons/

Icon 是属于 StatelessWidget。

```dart
// 配置  pubspec.yaml 中这句就可以使用Material风格内置的Icons了
flutter:
  uses-material-design: true
```

#### Icon Widget 构造方法和属性
```dart
const Icon(
  IconData icon, 
  {
    Key key,
    // 图标大小，默认为24px
    double size,
    // 图标颜色
    Color color,
    String semanticLabel,
    // 渲染图标的方向，前提需要IconData.matchTextDirection字段设置为true
    TextDirection textDirection
  }
)
```

#### Icon Widget 实例
```dart
Center(
  child: Column(
    children: <Widget>[
      /// flutter 自带的icon
      IconButton(
        icon: Icon(Icons.add_circle),
        iconSize: 30,
        // icon颜色
        color: Colors.greenAccent,
        // 按下时，button 水波纹效果
        splashColor: Colors.indigoAccent,
        onPressed: (){},
      ),
      
      /// Icon
      Icon(
        Icons.add_shopping_cart,
        size: 30.0,
        color: Colors.blueAccent,
      ),

      /// ImageIcon, 使用自已的png图片，有透明度。 需要在 pubspec.yaml 中配置图片文件
      ImageIcon(
        AssetImage('lib/images/icon_wx.png'),
        size: 50.0,
        color: Colors.green,
      ),
    ],
  ),
),
```
效果为：
{% img /images/2019-08-14/flutter_study_04.png %}

---

### AlertDialog Widget

> AlertDialog Widget 主要是实现对话框效果。Flutter 中 dialog 一般分为：AlertDialog（用于警告提示对话框）、SimpleDialog（有列表选项的对话框）、CupertinoAlertDialog（iOS 风格的 alert dialog）、CupertinoDialog（iOS 风格的对话框）。

AlertDialog Widget 是一个 `StatelessWidget`。继承关系如下：

**Object > Diagnosticable > DiagnosticableTree > Widget > StatelessWidget > AlertDialog**

Flutter 自带这以下几个弹出对话框方法。

- showDialog：弹出 Material 风格对话框
- showCupertinoDialog：弹出 iOS 样式对话框
- showGeneralDialog：弹出自定义的对话框，默认状态下弹出的窗口点击空白处不消失
- showAboutDialog：弹出关于页面适用的对话框

#### AlertDialog 构造方法和属性
```dart
const AlertDialog({
  Key key,
  Widget title,
  EdgeInsetsGeometry titlePadding,
  TextStyle titleTextStyle,
  Widget content,
  EdgeInsetsGeometry contentPadding: const EdgeInsets.fromLTRB(24.0, 20.0, 24.0, 24.0),
  TextStyle contentTextStyle,
  // 操作按钮
  List<Widget> actions,
  Color backgroundColor,
  // 设置阴影大小
  double elevation,
  String semanticLabel,
  ShapeBorder shape
})
```

#### AlertDialog 实例

```dart
// AlertDialog 标题、内容、操作按钮
Future<void> dialog1(BuildContext context) async {
  return showDialog<void>(
    context: context,
    // 点击周围空白区域对话框是否消失
    barrierDismissible: false,
    builder: (BuildContext context) {
      return AlertDialog(
        title: Text("提示"),
        content: new Text("是否退出"),
        actions: <Widget>[
          new FlatButton(
            onPressed: () => Navigator.of(context).pop(false),
            child: new Text("取消")),
          new FlatButton(
            onPressed: () {
              Navigator.of(context).pop(true);
            },
            child: new Text("确定")
          ),
        ],
      );
    });
}


// 自定义Dialog
Future<void> dialog2(BuildContext context) async {
  return showDialog<void>(
      context: context,
      builder: (BuildContext context) {
        return Center(
          child: Text(
            '正在加载...',
            style: TextStyle(
              color: Colors.black,
              backgroundColor: Colors.white,
              decoration: TextDecoration.none
            ),
          ),
        );
      });
}

// AboutDialog
Future<void> dialog3(BuildContext context) async {
  return showAboutDialog(
    context: context,
    applicationName: "应用名称",
    applicationVersion: "1.0.0",
    applicationIcon: Icon(Icons.apps),
    applicationLegalese: "内容"
  );
}

// AlertDialog 标题、内容、操作按钮
Future<void> dialog4(BuildContext context) async {
  return showCupertinoDialog<void>(
    context: context,
    // 点击周围空白区域对话框是否消失
    // barrierDismissible: false,
    builder: (BuildContext context) {
      return CupertinoAlertDialog(
        title: Text("提示"),
        content: new Text("是否退出"),
        actions: <Widget>[
          new FlatButton(
            onPressed: () => Navigator.of(context).pop(false),
            child: new Text("取消")),
          new FlatButton(
            onPressed: () {
              Navigator.of(context).pop(true);
            },
            child: new Text("确定")
          ),
        ],
      );
    });
}
```


---
[相关代码](https://github.com/SeptemberMaples/study-section/tree/flutter_study/flutter_study/lib/flutter_04)


---
参考：
- [flutter官网](https://api.flutter.dev/flutter/widgets/Text-class.html)
- 【Flutter—从入门到实践(by Ray)】学习笔记