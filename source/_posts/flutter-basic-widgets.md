---
title: Flutter学习-03：基础widgets - Text、Image、Button
date: 2019-08-13 10:20:14
tags:
  - flutter
  - widget
categories:
  - Flutter
---

### Text Widget

> Text widget 可以用来在应用内创建带样式的文本。功能类似于HTML的文本标签，属于基础widget。

`Text`widget 是个 StatelessWidget，也就是无状态组件。其继承关系如下：

**Text -> StatelessWidget -> Widget -> DiagnosticableTree -> Diagnosticable -> Object**

Text widget包含两个构造方法：`Text`和`Text.rich`;

<!-- more -->

#### Text构造方法及属性

```dart
Text(
  //要显示的文字内容。非空的，必须有的参数，传入要显示的 String 类型的字符串
  String data, 
  { 
    //key类似于id
    Key key, 
    //文字显示样式和属性
    TextStyle style, 
    StrutStyle strutStyle, 
    //文字对齐方式
    TextAlign textAlign, 
    //文字显示方向
    TextDirection textDirection, 
    //设置语言环境
    Locale locale, 
    //是否自动换行
    bool softWrap, 
    //文字溢出后处理方式
    TextOverflow overflow, 
    //字体缩放
    double textScaleFactor, 
    //最大显示行数。配合 overflow，实现...
    int maxLines, 
    //图像的语义描述，用于向Andoid上的TalkBack和iOS上的VoiceOver提供图像描述
    String semanticsLabel, 
    // 组件的显示宽度。有三个值：longestLine / parent / values 
    TextWidthBasis textWidthBasis 
  }
)
```
其中`style`属于是UI里最常用到的，传入`TextStyle`对象。些对象包含的属性：
```dart
TextStyle(
  {
    //是否继承父类组件属性
    bool inherit: true, 
    //字体颜色
    Color color, 
    // 背景色。 与 background 不能同时存在
    Color backgroundColor, 
    //文字大小，默认14px
    double fontSize, 
    //字体粗细
    FontWeight fontWeight, 
    //字体样式,normal或italic
    FontStyle fontStyle, 
    //字母间距，默认为0，负数间距缩小，正数间距增大
    double letterSpacing, 
    //单词间距，默认为0，负数间距缩小，正数间距增大
    double wordSpacing, 
    //字体基线
    TextBaseline textBaseline, 
    //行高
    double height, 
    //设置区域
    Locale locale, 
    //前景色
    Paint foreground, 
    //背景色
    Paint background, 
    //阴影
    List<Shadow> shadows, 
    List<FontFeature> fontFeatures, 
    //文字划线，下换线等装饰
    TextDecoration decoration, 
    //划线颜色
    Color decorationColor, 
    //划线样式，虚线、实线等样式
    TextDecorationStyle decorationStyle,
    //划线样式，粗细 
    double decorationThickness, 
    //描述信息
    String debugLabel, 
    //字体
    String fontFamily, 
    List<String> fontFamilyFallback, 
    String package 
  }
)
```

#### Text.rich()构造方法及属性

`Text.rich()`作用就是可以在 Text 里加入一些 Span 标签，对某部分文字进行个性化改变样式，如加入 @ 符号，加入超链接、变色等等。Text.rich(…) 等价于 RichText(...)

```dart
const Text.rich(
  // 样式片段标签TextSpan
  InlineSpan textSpan, 
  {
    Key key,
    TextStyle style,
    StrutStyle strutStyle,
    TextAlign textAlign,
    TextDirection textDirection,
    Locale locale,
    bool softWrap,
    TextOverflow overflow,
    double textScaleFactor,
    int maxLines,
    String semanticsLabel,
    TextWidthBasis textWidthBasis
  }
)

RichText({
  Key key,
  @required InlineSpan text,
  TextAlign textAlign: TextAlign.start,
  TextDirection textDirection,
  bool softWrap: true,
  TextOverflow overflow: TextOverflow.clip,
  double textScaleFactor: 1.0,
  int maxLines,
  Locale locale,
  StrutStyle strutStyle,
  TextWidthBasis textWidthBasis: TextWidthBasis.parent
})
```

样式标签 `TextSpan` 的构造方法：
```dart
const TextSpan({
  //样式片段
  this.style,
  //要显示的文字
  this.text,
  //样式片段TextSpan数组，可以包含多个TextSpan
  this.children,
  //用于手势进行识别处理,如点击跳转
  this.recognizer,
})
```

#### Text widget 实例

Text widget的常用方法和属性上面介绍了， 现在来具体操作一下。

```dart
// 核心代码
body: Container(
  child: Column(
    children: <Widget>[
      // 最基础用法
      Text('Text 最基础用法'),
      // TextStyle 字体相关、字号、颜色、透明度、下划线等
      Text(
        '设置文字样式',
        style: TextStyle(
          fontWeight: FontWeight.bold,
          fontSize: 24,
          color: Colors.green,
          backgroundColor: Colors.black87,
          // background: Paint()..color,  // 同上面的backgroundColor
          decoration: TextDecoration.underline,
          decorationThickness: 2,
          letterSpacing: 5.0
        ),
        textWidthBasis: TextWidthBasis.longestLine,
      ),
      // 换行、裁剪
      Text(
        '文字换行，超出的文字用省略号表示.文字换行，超出的文字用省略号表示.文字换行，超出的文字用省略号表示.文字换行，超出的文字用省略号表示',
        maxLines: 2,
        overflow: TextOverflow.ellipsis,
        style: TextStyle(
          fontSize: 16,
          fontWeight: FontWeight.bold,
        ),
      ),
      // Text.rich()
      Text.rich(TextSpan(
        text: 'TextSpan',
        style: TextStyle(
          color: Colors.orange,
          fontSize: 30.0,
          decoration: TextDecoration.none,
        ),
        children: <TextSpan>[
          new TextSpan(
            text: '拼接1',
            style: new TextStyle(
              color: Colors.teal,
            ),
          ),
          new TextSpan(
            text: '拼接2',
            style: new TextStyle(
              color: Colors.teal,
            ),
          ),
          new TextSpan(
            text: '拼接3有点击事件',
            style: new TextStyle(
              color: Colors.yellow,
            ),
            recognizer: new TapGestureRecognizer()
              ..onTap = () {
                //增加一个点击事件
                print(
                    '@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@');
              },
          ),
        ],
      )),
    ],
  ),
),
```

效果如下：
{% img /images/2019-08-13/flutter_study_01.png %}

---

### Image Widget

> Image Widget 在 Flutter 里是用来负责显示图片的一个组件，功能类似于 Android的ImageView、Html 的一些image 标签等等，属于基础组件。

Image 是个 StatefulWidget，也就是有状态组件。继承关系如下：

**Image -> StatefulWidget -> Widget -> DiagnosticableTree -> Diagnosticable -> Object**

Image Widget 有5个构造方法和多个属性。 

Image 支持 5 种方式加载图片:
- Image：通过 ImageProvider 来加载图片
- Image.network：用来加载网络图片
- Image.file：用来加载本地 File 文件图片
- Image.asset：用来加载项目内资源图片
- Image.memory：用来加载 Uint8List 资源图片/内存图片

其中这几种其实都是通过 ImageProvider 来从不同源加载图片的，封装类有：NetworkImage、FileImage、AssetImage、ExactAssetImage、MemoryImage。我们既可以用这几个构造方法加载图片，也可以使用这几个封装类来加载。

5个构造方法的使用及属性：
```dart
//通过ImageProvider来加载图片
const Image({
    Key key,
    // ImageProvider，图像显示源
    @required this.image,
    this.semanticLabel,
    this.excludeFromSemantics = false,
    //显示宽度
    this.width,
    //显示高度
    this.height,
    //图片的混合色值
    this.color,
    //混合模式
    this.colorBlendMode,
    //缩放显示模式
    this.fit,
    //对齐方式
    this.alignment = Alignment.center,
    //重复方式
    this.repeat = ImageRepeat.noRepeat,
    //当图片需要被拉伸显示的时候，centerSlice定义的矩形区域会被拉伸，类似.9图片
    this.centerSlice,
    //类似于文字的显示方向
    this.matchTextDirection = false,
    //图片发生变化后，加载过程中原图片保留还是留白
    this.gaplessPlayback = false,
    //图片显示质量
    this.filterQuality = FilterQuality.low,
  })

// 加载网络图片，封装类：NetworkImage
Image.network(
    //路径
    String src, 
   {
    Key key,
    //缩放
    double scale = 1.0,
    this.semanticLabel,
    this.excludeFromSemantics = false,
    this.width,
    this.height,
    this.color,
    this.colorBlendMode,
    this.fit,
    this.alignment = Alignment.center,
    this.repeat = ImageRepeat.noRepeat,
    this.centerSlice,
    this.matchTextDirection = false,
    this.gaplessPlayback = false,
    this.filterQuality = FilterQuality.low,
    Map<String, String> headers,
  })

// 加载本地File文件图片，封装类：FileImage
Image.file(
    //File对象
    File file, 
  {
    Key key,
    double scale = 1.0,
    this.semanticLabel,
    this.excludeFromSemantics = false,
    this.width,
    this.height,
    this.color,
    this.colorBlendMode,
    this.fit,
    this.alignment = Alignment.center,
    this.repeat = ImageRepeat.noRepeat,
    this.centerSlice,
    this.matchTextDirection = false,
    this.gaplessPlayback = false,
    this.filterQuality = FilterQuality.low,
  })

// 加载本地资源图片,例如项目内资源图片
// 需要把图片路径在pubspec.yaml文件中声明一下，如：
// assets:
//      - packages/fancy_backgrounds/backgrounds/background1.png
// 封装类有：AssetImage、ExactAssetImage
Image.asset(
    //文件名称，包含路径
    String name, 
  {
    Key key,
    // 用于访问资源对象
    AssetBundle bundle,
    this.semanticLabel,
    this.excludeFromSemantics = false,
    double scale,
    this.width,
    this.height,
    this.color,
    this.colorBlendMode,
    this.fit,
    this.alignment = Alignment.center,
    this.repeat = ImageRepeat.noRepeat,
    this.centerSlice,
    this.matchTextDirection = false,
    this.gaplessPlayback = false,
    String package,
    this.filterQuality = FilterQuality.low,
  })

// 加载Uint8List资源图片/从内存中获取图片显示
// 封装类：MemoryImage
Image.memory(
    // Uint8List资源图片
    Uint8List bytes,
  {
    Key key,
    double scale = 1.0,
    this.semanticLabel,
    this.excludeFromSemantics = false,
    this.width,
    this.height,
    this.color,
    this.colorBlendMode,
    this.fit,
    this.alignment = Alignment.center,
    this.repeat = ImageRepeat.noRepeat,
    this.centerSlice,
    this.matchTextDirection = false,
    this.gaplessPlayback = false,
    this.filterQuality = FilterQuality.low,
  })
```

#### colorBlendMode 混合模式
主要用在使用`canvas`画图时。
```dart
// Flutter一共29种混合模式
enum BlendMode {
  clear,src,dst,srcOver,dstOver,srcIn,dstIn,srcOut,dstOut,srcATop,dstATop,xor,plus，modulate,screen,overlay,darken,lighten,colorDodge,colorBurn,hardLight,softLight,difference,exclusion,multiply,hue,saturation,color,luminosity,
}
```
显示效果：
{% img /images/2019-08-13/flutter_study_02.png %}

#### fit 缩放显示模式
```dart
//主要有7种
enum BoxFit {
  fill,
  contain,
  cover,
  fitWidth,
  fitHeight,
  none,
  scaleDown,
}
```
- fill 填充模式，拉伸充满
- contain，全图显示，显示原比例，不需充满
- cover，显示可能拉伸，可能裁剪，充满
- fitWidth，显示可能拉伸，可能裁剪，宽度充满
- fitHeight，显示可能拉伸，可能裁剪，高度充满
- none，对图片不做任何处理显示，高出控件大小就裁剪，否则不处理
- scaleDown，全图显示，显示原比例，不允许显示超过源图片大小，可小不可大

#### Image Widget 实例
```dart
  // 部分代码
  body: CustomScrollView(
       slivers: <Widget>[
         SliverPadding(
           padding: EdgeInsets.all(20.0),
           sliver: SliverList(
             delegate: SliverChildListDelegate(
               <Widget>[
                 // 读取网络图片
                  Image.network(imageUrl),
                  Text(
                    "读取网络图片",
                    textAlign: TextAlign.center,
                  ),
                  Image(
                    image: NetworkImage(imageUrl),
                    height: 100,
                    fit: BoxFit.contain,
                  ),
                  Text(
                    "用NetworkImage读取网络图片",
                    textAlign: TextAlign.center,
                  ),

                  //从项目目录里读取图片，需要在pubspec.yaml注册路径
                  Image.asset("lib/images/assets_image.jpg"),
                  Text(
                    "项目asset目录里读取",
                    textAlign: TextAlign.center,
                  ),
                  Image(
                    image: AssetImage("lib/images/assets_image.jpg"),
                    width: 200,
                    height: 130,
                  ),
                  Text(
                    "AssetImage读取",
                    textAlign: TextAlign.center,
                  ),

                  /// 从文件读取图片
                  // Image.file(
                  //   File('/sdcard/img.png'),
                  //   width: 200,
                  //   height: 80,
                  // ),
                  // Image(
                  //   image: FileImage(File('/sdcard/img.png')),
                  // ),

                  ///内存中读取byte数组图片
                  /// Image.memory(bytes)
                  /// Image(
                  ///   image: MemoryImage(bytes),
                  /// ),
                   
                  ///加入占位图的加载图片
                  // FadeInImage(
                  //   placeholder: AssetImage("lib/images/assets_image.jpg"),
                  //   image: FileImage(File('/sdcard/img.png')),
                  // ),
                  FadeInImage.assetNetwork(
                    placeholder: "lib/images/assets_image.jpg",
                    image: imageUrl,
                  ),
                  Text(
                    "加入占位图的加载图片",
                    textAlign: TextAlign.center,
                  ),

                  ///加载圆角图片
                  CircleAvatar(
                    backgroundColor: Colors.brown.shade800,
                    child: Text("圆角头像"),
                    backgroundImage: AssetImage("lib/images/assets_image.jpg"),
                    radius: 100.0,
                  ),
                  Text(
                    "加载圆角图片",
                    textAlign: TextAlign.center,
                  ),

                  ImageIcon(NetworkImage(imageUrl)),
                  Text(
                    "ImageIcon",
                    textAlign: TextAlign.center,
                  ),
                  ClipRRect(
                    child: Image.network(
                      imageUrl,
                      scale: 8.5,
                      fit: BoxFit.cover,
                    ),
                    borderRadius: BorderRadius.only(
                      topLeft: Radius.circular(20),
                      topRight: Radius.circular(20),
                    ),
                  ),
                  Text(
                    "ClipRRect",
                    textAlign: TextAlign.center,
                  ),

                   Container(
                    width: 120,
                    height: 60,
                    decoration: BoxDecoration(
                      shape: BoxShape.rectangle,
                      borderRadius: BorderRadius.circular(10.0),
                      image: DecorationImage(
                          image: NetworkImage(imageUrl), fit: BoxFit.cover),
                    ),
                  ),
                  Text(
                    "BoxDecoration",
                    textAlign: TextAlign.center,
                  ),
                  ClipOval(
                    child: Image.network(
                      imageUrl,
                      scale: 8.5,
                    ),
                  ),
                  Text(
                    "ClipOval",
                    textAlign: TextAlign.center,
                  ),
               ]
             ),
           ),
         )
       ],
      ),
```

{% img /images/2019-08-13/flutter_study_03.png %}
---
[相关代码]()


---
参考：
- [flutter官网](https://api.flutter.dev/flutter/widgets/Text-class.html)
- 【Flutter—从入门到实践(by Ray)】学习笔记