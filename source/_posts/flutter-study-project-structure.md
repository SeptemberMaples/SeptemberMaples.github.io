---
title: Flutter学习-01：项目结构及配置文件
date: 2019-08-09 11:26:48
tags:
  - flutter
categories:
  - Flutter
---

> 如果你已经对Dart语言有了了解，并按官网配置了Flutter本地开发环境，然后就可以本地建一个Flutter项目，愉快地开始学习了。

### 新建项目
我用 vs code 来开发，需要安装 `Dart`和`Flutter`两个扩展插件。 然后调出命令面板(`ctrl/command+shift+p`)，输入`flutter`，选择新建项目，输入项目名后，会自动初始化一个新项目。
{% img /images/2019-08-09/flutter_study_01.jpg %}

<!-- more -->

### 项目结构
打开项目，能看到默认的结构目录：{% img /images/2019-08-09/flutter_study_02.png %}
主要包含：android、ios、lib、test 几个目录以及 pubspec.yaml 配置文件等。

#### android、ios目录
项目编译后，Flutter代码会整合到android、ios目录中。这两个目录是结构完整的Android\iOS应用，可分别用Android Studio或Xcode打开进行编译、运行、开发、调试等。

#### lib目录
这个目录下是我们flutter项目核心代码存放的地方，放置dart语言的类源码文件（main.dart）。lib下的源码会编译到Android和iOS平台进行渲染。

> 默认有一个 main.dart 类，这个 main.dart 类名不可以修改,必须放置在 lib 根目录，它是我们项目的入口文件，入口类。

#### test目录
主要是放测试用例文件。

### Flutter 配置文件

#### pubspec.yaml
这个是我们的 Flutter 项目的配置文件，我们在这里可以配置引用第三方插件库、添加 assets 图片资源、font 字体资源、音视频资源路径等等。整个文件是参照 YAML（YAML Ain't a Markup Language）语法规范进行定义的格式，其实就是通过缩进来形成结构目录，非常简单，因此就不过多描述语法了。我们看下 pubspec.yaml 配置文件的内容和结构：
```yaml
//项目名称：要用英文，类似于Android中的包名，如果它修改了整个项目的引入的路径都要修改
//所以一般确定了就不要修改
name: flutter_study
//项目描述
description: A new Flutter project.

//版本号，这个会覆盖对应Android和IOS的应用版本号
//+号前对应Android的versionCode，+号后对应Android的versionName
//+号前对应IOS的CFBundleVersion，+号后对应IOS的CFBundleShortVersionString
version: 1.0.0+1

//表示项目的编译环境要求为dart sdk版本号在2.1.0和3.0.0之间
environment:
  sdk: ">=2.1.0 <3.0.0"


//项目的依赖插件库
//Flutter插件库在这里查找引用：https://pub.dartlang.org/flutter
dependencies:
  flutter:
    sdk: flutter
//我们可以在这里引入插件库
  cupertino_icons: ^0.1.2
  flutter_webview_plugin: ^0.3.1

dev_dependencies:
  flutter_test:
    sdk: flutter

//flutter相关配置
flutter:
//是否使用material图标，建议为true
  uses-material-design: true

  //配置项目文件里的图片路径
  //如果需要使用项目目录内附带的图片、音视频等资源，必须在这里配置定义
  assets:
    - images/a_dot_burr.jpeg
    - images/a_dot_ham.jpeg

  //字体文件资源相关配置
  fonts:
    - family: Schyler
      fonts:
        - asset: fonts/Schyler-Regular.ttf
        - asset: fonts/Schyler-Italic.ttf
          style: italic
    - family: Trajan Pro
      fonts:
        - asset: fonts/TrajanPro.ttf
        - asset: fonts/TrajanPro_Bold.ttf
          weight: 700

//下面这几项一般只有在编写插件库发布到Dart Pub时才写，一般不用写  
//作者
authors:
- Natalie Weizenbaum <nweiz@google.com>
- Bob Nystrom <rnystrom@google.com>
//主页
homepage: https://example-pet-store.com/newtify
//文档地址
documentation: https://example-pet-store.com/newtify/docs
//发布到
publish_to: none
```

#### pubspec.lock

里面指明了我们的 pubspec.yaml 等依赖包和项目依赖的库的具体版本号，我们可以在这里查看我们项目引用的依赖的具体版本号等信息，如果某个配置文件丢失，可以通过这个文件重新下载和恢复依赖库。为自动生成文件。

#### .packages

里面放置了项目依赖的库的具体在本机电脑上的绝对路径。为自动生成文件，如果项目出错或者无法找到某个库，可以把这个文件删除，重新自动配置即可。

#### .metadata

里面记录了我们项目的属性信息，如使用 Flutter SDK 哪个分支开发的，项目属性等，用于切换分支、升级 SDK 使用。自动生成，无需修改删除。

以上介绍的是Flutter项目的结构。现在来看看lib里的代码结构。

### Flutter 代码结构
默认 lib 下有一个 main.dart 类，这个 main.dart 类名字不可以修改，必须放置在 lib 根目录，它是我们项目的入口文件，入口类。实际开发中，可按功能来组织代码分类，如：assets、utils、widgets、ui。

来看下默认 main.dart 文件的内容：
```dart
import 'package:flutter/material.dart';

//void main()为入口方法,main.dart文件独有
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  //应用的最顶层入口
  @override
  Widget build(BuildContext context) {
    //这里使用了MaterialApp脚手架，当然也可以使用WidgetApp，建议入口使用MaterialApp。
    //直接定义一个容器布局也可以
    return MaterialApp(
      //标题
      title: 'Flutter Demo',
      theme: ThemeData(
        //可以自定义配置主题色调
        primarySwatch: Colors.blue,
      ),
      //页面
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

//使用StatefulWidget有状态Widget
class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  //创建State状态
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    //每次执行这个方法_counter加一，然后用setState方法进行UI刷新
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    //Scaffold布局脚手架组件
    return Scaffold(
      //标题栏Widget组件
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        //定义了一个Button，点击执行_incrementCounter()方法
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}
```

代码整体风格就是 React 组件化响应式风格，类似于 DOM 树结构，通过小的 Widget 来组成一个大的页面。和我一样看的有点懵？没关系，接下来看了基础的 widgets 后再看就很清晰了。 其实就是创建一个容器，放入不同的组件组合，简化如下：
```dart
import 'package:flutter/material.dart';

//void main()为入口方法,main.dart文件独有
void main() => runApp(MyMainApp());

// 一个有状态Widget
class MyMainApp extends StatefulWidget {
  //这里可以简写为：State<StatefulWidget> createState() => _MyMainPageState();
  @override
  State<StatefulWidget> createState() {
    return _MyMainPageState();
  }
}

class _MyMainPageState extends State<MyMainApp> {
  @override
  void initState() {
    super.initState();
  }

  // 布局
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      //页面
      home: Scaffold(
        appBar: AppBar(
          title: Text('标题'),
        ),
        body: Center(
          child: Text("我是内容"),
        ),
      ),
    );
  }
}
```
{% img /images/2019-08-09/flutter_study_03.png %}

---
### 补充：
<font color='red'>**上面的代码可以看出，`StatefulWidget` 和 `State` 是独立的对象`, 这是为什么？**</font>
{% img /images/2019-08-09/flutter_study_04.png %}

在 Flutter 中，这两种类型的对象具有**不同的生命周期。Widget 是临时对象，用于构造应用当前状态的展示。而 State 对象在调用 build() 之间是持久的，以此来存储信息**。

在复杂的应用中，widget 层次不同的部分可能负责不同的关注点；例如，一个 widget 可能呈现复杂的用户界面，来收集像日期或位置这样特定的信息，而另一个 widget 可能使用该信息来改变整体的展现。（拆分的更细化）

widget 通过回调得到状态改变的通知，同时当前状态通知给其他 widget 用于显示。重定向这一流程的共同父级是 State。

---
[<font color='blue'>相关代码</font>](https://github.com/SeptemberMaples/study-section/tree/flutter_study/flutter_study/lib/flutter_01)


___
本文是【Flutter—从入门到实践(by Ray)】的学习笔记