---
title: flutter学习-08：列表滚动Widgets
date: 2019-09-03 13:55:39
tags:
  - flutter
  - widget
categories:
  - Flutter
---

前面学习了布局相关的Widgets，我们知道`Flex`、`Row`、`Column`会有一个问题就是超出父元素的宽高时会提示错误（[详见](/2019/08/20/flutter-layout-widgets-2/)），`Wrap`、`Flow`这样的流式布局可以根据父元素的宽度自动换行，但是超出父元素的高度的内容不会显示出来，相当于CSS中的`overflow: hidden`了，也就是没有出现滚动条。列表滚动在实际应用中是非常常用的，Flutter的滚动组件容器主要有：
- ListView Widget
- ScrollView Widget
- CustomScrollView Widget

<!-- more -->

### ListView Widget

`ListView` 主要实现**线性列表布局**，可以横向或者纵向。`ListView` 的继承关系：
ListView -> BoxScrollView -> ScrollView
`ListView` 也是继承自 `ScrollView` 组件，扩展了` ScrollView` 的特点。

 ListView 的构造方法：
 ```dart
  ListView({
    Key key,
    // 滚动排列方向
    Axis scrollDirection: Axis.vertical,
    bool reverse: false,
    ScrollController controller,
    bool primary,
    // 物理滑动响应动画
    ScrollPhysics physics,
    // 是否根据子widget的总高度/长度来设置ListView的长度，默认值为false
    bool shrinkWrap: false,
    EdgeInsetsGeometry padding,
    // item固定高度
    double itemExtent,
    // 是否将item包裹在AutomaticKeepAlive widget中
    bool addAutomaticKeepAlives: true,
    // 是否将item包裹在RepaintBoundary中
    bool addRepaintBoundaries: true,
    bool addSemanticIndexes: true,
    double cacheExtent,
    // 子item元素
    List<Widget> children: const [],
    int semanticChildCount,
    DragStartBehavior dragStartBehavior: DragStartBehavior.start
  })
 ```

 **除此之外，还有三个构造方法：**
 - [ListView.builder](https://api.flutter.dev/flutter/widgets/ListView/ListView.builder.html)
 - [ListView.custom](https://api.flutter.dev/flutter/widgets/ListView/ListView.custom.html)
 - [ListView.separated](https://api.flutter.dev/flutter/widgets/ListView/ListView.separated.html)

 
 **ListView 实例**

```dart
//ListView最简单的用法
ListView(
  shrinkWrap: true,
  padding: const EdgeInsets.all(20.0),
  children: <Widget>[
    const Text('I\'m dedicating every day to you'),
    const Text('Domestic life was never quite my style'),
    const Text('When you smile, you knock me out, I fall apart'),
    const Text('And I thought I was so smart'),
  ],
),

// 稍复杂用法
// 定义一个List
List<String> items = <String>[
  'A',
  'B',
  'C',
  'D',
  'E',
  'F',
  'G',
  'H',
  'I',
  'J',
  'K',
  'L',
  'M',
  'N',
  'O',
];

// 定义个枚举来设置item显示几行及类型
enum _MaterialListType {
  oneLine,

  oneLineWithAvatar,

  twoLine,

  threeLine,
}

class ListViewSamples extends StatefulWidget {
  @override
  State<StatefulWidget> createState() {
    return ListViewSamplesState();
  }
}

class ListViewSamplesState extends State<ListViewSamples> {
  List widgets = [];

  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('ListView'),
      ),
      body: listView4(),
    );
  }

  ///最简单的ListView
  Widget listView1() {
    return ListView(
      children: <Widget>[
        Text(
          'data',
          style: TextStyle(fontSize: 30),
        ),
        Text(
          'data',
          style: TextStyle(fontSize: 30),
        ),
        Text(
          'data',
          style: TextStyle(fontSize: 30),
        ),
        Text(
          'data',
          style: TextStyle(fontSize: 30),
        ),
        Text(
          'data',
          style: TextStyle(fontSize: 30),
        ),
        Text(
          'data',
          style: TextStyle(fontSize: 30),
        ),
        Text(
          'data',
          style: TextStyle(fontSize: 30),
        ),
      ],
    );
  }

  ///动态封装ListView，使用ListTile作为item
  Widget listView2() {
    // listTiles为item布局集合
    Iterable<Widget> listTiles = items.map<Widget>((String string) {
      return getItem(string);
    });
    ListTile.divideTiles(context: context, tiles: listTiles);
    return ListView(
      children: listTiles.toList(),
    );
  }

  ///使用ListView.builder构造
  Widget listView3() {
    // item widget集合
    return ListView.builder(
      // 设置item数量
      itemCount: items.length,
      itemBuilder: (BuildContext context, int position) {
        return getItem(items.elementAt(position));
      },
    );
  }

  ///ListView.custom构建ListView
  Widget listView4() {
    ///SliverChildListDelegate：适用于有固定数量的item的List
    ///SliverChildBuilderDelegate:适用于不固定数量的item的List
    return ListView.custom(
      // 设置item构建属性
      childrenDelegate:
          SliverChildBuilderDelegate((BuildContext context, int index) {
        // 返回item布局
        return ListTile(
          isThreeLine: true,
          dense: true,
          leading: ExcludeSemantics(
              child: CircleAvatar(child: Text(items.elementAt(index)))),
          title: Text('This item represents .'),
          subtitle: Text("$index"),
          trailing: Icon(Icons.info, color: Theme.of(context).disabledColor),
        );
      }, childCount: 13),
    );
  }

  ///ListView.separated构建ListView
  Widget listView5() {
    // 有分隔线
    return ListView.separated(
      // item数量
      itemCount: items.length,
      // 分隔线属性设置
      separatorBuilder: (BuildContext context, int index) {
        return Container(height: 1, color: Colors.pink);
      },
      // 构建item布局
      itemBuilder: (BuildContext context, int index) {
        return ListTile(
          isThreeLine: true,
          dense: true,
          leading: ExcludeSemantics(
              child: CircleAvatar(child: Text(items.elementAt(index)))),
          title: Text('This item represents .'),
          subtitle: Text(items.elementAt(index)),
          trailing: Icon(Icons.info, color: Theme.of(context).disabledColor),
        );
      },
    );
  }

  // 用来获取item布局的方法
  Widget getItem(String item) {
    // if (i.isOdd) {
    //   return Divider();
    // }
    return GestureDetector(
      child: Padding(
        padding: EdgeInsets.all(10.0),
        child: ListTile(
            dense: true,
            title: Text('Two-line ' + item),
            trailing: Radio<_MaterialListType>(
              value: _MaterialListType.twoLine,
              groupValue: _MaterialListType.twoLine,
              onChanged: changeItemType,
            )),
      ),
      onTap: () {
        setState(() {
          // print('row $i');
        });
      },
      onLongPress: () {},
    );
  }

  void changeItemType(_MaterialListType type) {
    print("changeItemType");
  }
}
```

 {% img /images/2019-09-03/20190903171814.png %}
 
---

 ### GridView Widget

 `GridView` 的用法和 `ListView` 类似，是实现网格列表的 item 排列效果。 `GridView` 的继承关系：
**GridView -> BoxScrollView -> ScrollView**
GridView 也是继承自 ScrollView 组件，扩展了 ScrollView 的特点。

`GridView` 的构造方法：
```dart
GridView({
  Key key,
  Axis scrollDirection: Axis.vertical,
  bool reverse: false,
  ScrollController controller,
  bool primary,
  ScrollPhysics physics,
  bool shrinkWrap: false,
  EdgeInsetsGeometry padding,
  // 控制GridView的item如何排列
  @required SliverGridDelegate gridDelegate,
  bool addAutomaticKeepAlives: true,
  bool addRepaintBoundaries: true,
  bool addSemanticIndexes: true,
  double cacheExtent,
  List<Widget> children: const [],
  int semanticChildCount
})
```
**还有其它四种构造方法：**
- [GridView.builder](https://api.flutter.dev/flutter/widgets/GridView/GridView.builder.html)
- [GridView.count](https://api.flutter.dev/flutter/widgets/GridView/GridView.count.html)
- [GridView.custom](https://api.flutter.dev/flutter/widgets/GridView/GridView.custom.html)
- [GridView.extent](https://api.flutter.dev/flutter/widgets/GridView/GridView.extent.html)

**GridView 实例**
```dart
// 简单应用 GridView.count 构造
  Widget gridView1() {
    return GridView.count(
      primary: false,
      padding: const EdgeInsets.all(20.0),
      crossAxisSpacing: 10.0,
      // 每行多少个item
      crossAxisCount: 2,
      mainAxisSpacing: 10,
      children: <Widget>[
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
      ],
    );
  }

  ///  GridView构造
  Widget gridView2() {
    return GridView(
      padding: EdgeInsets.all(20.0),
      ///可以固定设置每行item个数
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        crossAxisSpacing: 10,
        mainAxisSpacing: 10,
      ),
      children: <Widget>[
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
        Image.asset(
          'lib/images/assets_image.jpg',
          fit: BoxFit.cover,
        ),
      ],
    );
  }


  /// GridView.builder 构造
  Widget gridView3() {
    return GridView.builder(
      padding: EdgeInsets.all(10),
      // item总数
      itemCount: 30,
      // 构建item
      itemBuilder: (BuildContext context, int index) {
        // GridTile可以构造带有头部、底部、中间内容的item
        return GridTile(
          header: GridTileBar(
            title: Text(
              'header',
              style: TextStyle(color: Colors.green)
            ),
            subtitle: Text(
              'subtitle',
              style: TextStyle(color: Colors.blueGrey)
            ),
          ),
          child: Image.asset(
            'lib/images/assets_image.jpg',
            fit: BoxFit.cover,
          ),
          footer: GridTileBar(
            title: Text(
              'bottom',
              style: TextStyle(
                backgroundColor: Colors.black
              )
            ),
          ),
        );
      },

      // GridView排列属性设置
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        crossAxisSpacing: 10.0,
        mainAxisSpacing: 10.0
      ),
    );
  } 


  /// GridView.custom  构建
  Widget gridView4() {
    return GridView.custom(
      // 设置GridView属性
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
        crossAxisSpacing: 10,
        mainAxisSpacing: 10
      ),

      // 设置item属性
      childrenDelegate: SliverChildBuilderDelegate((BuildContext context, int index) {
        return Container(
          child: Text('data $index'),
          color: Colors.green,
        );
      }, childCount: 20)
    );
  }


  ///GridView.extent构建GridView，根据最大宽度自动计算item数量 
  Widget gridView5() {
    return GridView.extent(
      // item最大宽度
      maxCrossAxisExtent: 180,
      crossAxisSpacing: 10,
      mainAxisSpacing: 10,
      childAspectRatio: 1,
      children: <Widget>[
        GridTile(
          child: Image.asset(
            'lib/images/assets_image.jpg',
            fit: BoxFit.cover,
          ),
        ),
        GridTile(
          child: Image.asset(
            'lib/images/assets_image.jpg',
            fit: BoxFit.cover,
          ),
        ),
        GridTile(
          child: Image.asset(
            'lib/images/assets_image.jpg',
            fit: BoxFit.cover,
          ),
        ),
      ],
    );
  }
```
 {% img /images/2019-09-03/20190903171815.png %}

---
 
 ### CustomScrollView Widget

 `CustomScrollView` 是一个可以自己通过 Flutter 里的 `sliver` 来组装滚动 Widget 的一个控件，里面可以放置任何我们需要滚动的 Widget，也是相对来说**最常使用**的一个滚动组件。

 `CustomScrollView` 的继承关系：
**CustomScrollView -> ScrollView -> StatelessWidget**
CustomScrollView 是一个*无状态*组件，继承自 ScrollView，也扩展了 ScrollView 的功能。

CustomScrollView 最大的特点就是内部的组装的滚动组件都是 Sliver 特性的，也就是必须是 Sliver 可滚动块的 Widget 才可以，如：CustomScrollView 内部可以放置 SliverList、SliverFixedExtentList、SliverGrid、SliverPadding、SliverAppBar、SliverToBoxAdapter 等组件。

**CustomScrollView 的构造方法：**
```dart
const CustomScrollView({
  Key key,
  Axis scrollDirection: Axis.vertical,
  bool reverse: false,
  ScrollController controller,
  bool primary,
  ScrollPhysics physics,
  bool shrinkWrap: false,
  Key center,
  double anchor: 0.0,
  double cacheExtent,
  List<Widget> slivers: const [],
  int semanticChildCount,
  DragStartBehavior dragStartBehavior: DragStartBehavior.start
})
```

**CustomScrollView widget 实例**
```dart
 @override
Widget build(BuildContext context) {
  return Material(
    child: CustomScrollView(
      // slivers里面放置sliver滚动块组件
      slivers: <Widget>[
        // 放置一个顶部的标题栏
        SliverAppBar(
          // title: Text('data'),
          // 是否固定在顶部
          pinned: true,
          // 展开高度
          expandedHeight: 250.0,
          // 可展开区域，通常是一个FlexibleSpaceBar
          flexibleSpace: FlexibleSpaceBar(
            title: const Text('CustomScrollView'),
            background: Image.asset('lib/images/assets_image.jpg',fit: BoxFit.cover,),
          ),
        ),

        // 放置一个SliverGrid Widget
        SliverGrid(
          // 设置Grid属性：
          // SliverGridDelegateWithMaxCrossAxisExtent：
          // 按照设置最大扩展宽度计算item个数
          // SliverGridDelegateWithFixedCrossAxisCount:
          // 可以固定设置每行item个数
          gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
            // item最大宽度
            maxCrossAxisExtent: 200.0,
            // 主轴item间隔
            mainAxisSpacing: 10.0,
            // 交叉轴item间隔
            crossAxisSpacing: 10.0,
            // item宽高比
            childAspectRatio: 4.0,
          ),
          // 设置item的布局及属性
          // SliverChildListDelegate：适用于有固定数量的item的List
          // SliverChildBuilderDelegate:适用于不固定数量的item的List
          delegate: SliverChildBuilderDelegate(
            (BuildContext context, int index) {
              return Container(
                alignment: Alignment.center,
                color: Colors.teal[100 * (index % 9)],
                child: Text('grid item $index'),
              );
            },
            // 20个item数量
            childCount: 20,
          ),
        ),
        // 指定item高度的List
        SliverFixedExtentList(
          // item固定高度
          itemExtent: 50.0,
          // 设置item布局和属性
          delegate: SliverChildBuilderDelegate(
            (BuildContext context, int index) {
              return Container(
                alignment: Alignment.center,
                color: Colors.lightBlue[100 * (index % 9)],
                child: Text('list item $index'),
              );
            },
            childCount: 20,
          ),
        )
      ],
    ),
  );
}
```

{% img /images/2019-09-03/20190904001.gif %}

---

### 其他

ScrollView 这个 Widget 并不直接单独使用，一般是使用它的扩展类，如 CustomScrollView、ListView、GridView 等等。

**滚动条样式设置**。从上面的效果图看出，一般的滚动类组件不显示滚动条，如果想显示滚动条，需要用 `Scrollbar` 进行包裹。

```dart
///Scrollbar
Widget scroll1() {
  return Scrollbar(
    child: ListView.separated(
      itemCount: 20,
      separatorBuilder: (BuildContext context, int index) {
        return Container(height: 1, color: Colors.black87);
      },
      itemBuilder: (BuildContext context, int index) {
        return ListTile(
          isThreeLine: true,
          dense: true,
          leading:
              ExcludeSemantics(child: CircleAvatar(child: Text('leading'))),
          title: Text('This item represents .'),
          subtitle: Text('subtitle'),
          trailing: Icon(Icons.info, color: Theme.of(context).disabledColor),
        );
      },
    ),
  );
}
```

还有单子元素的 ScrollView（`SingleChildScrollView`），类似于 Android 里的 ScrollView，内部只能包裹一个子控件。

---

[相关代码](https://github.com/SeptemberMaples/study-section/tree/flutter_study/flutter_study/lib/flutter_08)

 ---
 参考：
 - [flutter官网](https://api.flutter.dev/flutter/widgets/CustomScrollView-class.html)
 - 【Flutter—从入门到实践(by Ray)】学习笔记