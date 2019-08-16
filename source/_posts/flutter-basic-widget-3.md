---
title:  Flutter学习-05：基础widgets - Form 表单
date: 2019-08-15 18:08:40
tags:
  - flutter
  - widget
categories:
  - Flutter
---

> Form 表单和 HTML 的 Form 表单的作用基本一样，主要用于提交一系列表单信息，如注册、登录信息表单的提交等操作。 HTML中 Form 表单会包含一系列输入框、选择框等，Flutter中对应的是 `TextField` widget。

### TextFiled Widget

TextField 属于 `StatefulWidget`，功能是提示信息、输入框接收的数据类型、输入框监听事件等。

#### TextField 构造方法和属性
```dart
const TextField({
  Key key,
  // 输入框的控制器，监听器
  TextEditingController controller,
  // 焦点控制的，控制是否获取和取消焦点
  FocusNode focusNode,
  // 输入框的装饰类（重要）
  InputDecoration decoration: const InputDecoration(),
  // 输入框输入类型，键盘显示类型
  TextInputType keyboardType,
   // 控制键盘上的动作按钮图标
  TextInputAction textInputAction,
  // 键盘大小写显示控制
  TextCapitalization textCapitalization: TextCapitalization.none,
  // 输入文本的样式
  TextStyle style,
  StrutStyle strutStyle,
  // 输入文本的对齐方式
  TextAlign textAlign: TextAlign.start,
  TextAlignVertical textAlignVertical,
  // 输入文本的方向
  TextDirection textDirection,
  bool readOnly: false,
  bool showCursor,
  // 是否自动获取焦点
  bool autofocus: false,
  // 是否是密码（是否遮盖输入内容，起保护作用）
  bool obscureText: false,
  // 是否自动更正
  bool autocorrect: true,
  // 最大行数
  int maxLines: 1,
  // 最小行数
  int minLines,
  bool expands: false,
  // 输入文本最大长度
  int maxLength,
  // 达到最大长度后：为true时会阻止输入，为false时不会阻止输入，但输入框会变红进行提示
  bool maxLengthEnforced: true,
  // 输入内容改变时的回调
  ValueChanged<String> onChanged,
  // 输入完成，按回车时调用
  VoidCallback onEditingComplete,
  // 输入完成，按回车时回调，回调里有参数：参数为输入的内容
  ValueChanged<String> onSubmitted,
  // 输入时校验，如只能输入手机号
  List<TextInputFormatter> inputFormatters,
  // 是否可用
  bool enabled,
  // 光标宽度
  double cursorWidth: 2.0,
  // 光标圆角
  Radius cursorRadius,
  // 光标颜色
  Color cursorColor,
  // 键盘样式，暗黑主题还是亮色主题
  Brightness keyboardAppearance,
  // 滚动区域padding
  EdgeInsets scrollPadding: const EdgeInsets.all(20.0),
  DragStartBehavior dragStartBehavior: DragStartBehavior.start,
  // 为true的时候长按会显示系统粘贴板的内容
  bool enableInteractiveSelection,
  // 点击事件
  GestureTapCallback onTap,
  InputCounterWidgetBuilder buildCounter,
  ScrollController scrollController,
  ScrollPhysics scrollPhysics
})
```

#### InputDecoration 
通过 `InputDecoration` 我们可以个性化定制和配置我们的 `TextField`,如输入框样式、icon图标、提示信息、错误提示信息等。其构造方法为：
```dart
const InputDecoration({
  //左侧显示的图标
  this.icon,
  // 输入框顶部提示信息
  this.labelText,
  // 提示信息样式
  this.labelStyle,
  // 输入框底部提示信息
  this.helperText,
  this.helperStyle,
  // 输入框内提示信息
  this.hintText,
  this.hintStyle,
  this.hintMaxLines,
  // 输入框底部错误提示信息
  this.errorText,
  this.errorStyle,
  this.errorMaxLines,
  this.hasFloatingPlaceholder = true,
  this.isDense,
  // 输入内容边距
  this.contentPadding,
  // 输入框内部左侧图标
  this.prefixIcon,
  // 输入框内部左侧自定义提示Widget
  this.prefix,
  // 输入框内部左侧提示文本
  this.prefixText,
  this.prefixStyle,
  // 输入框内部后缀图标
  this.suffixIcon,
  this.suffix,
  this.suffixText,
  this.suffixStyle,
  // 输入框右下角字数统计信息
  this.counter,
  this.counterText,
  this.counterStyle,
  // 是否填充颜色
  this.filled,
  // 输入框填充颜色
  this.fillColor,
  this.errorBorder,
  this.focusedBorder,
  this.focusedErrorBorder,
  this.disabledBorder,
  this.enabledBorder,
  // 输入框边框样式
  this.border,
  this.enabled = true,
  this.semanticCounterText,
  this.alignLabelWithHint,
})
```

#### TextInputType
如同HTML中input使用type限制输入类型（text、password、email等），Flutter TextField 使用 `TextInputType`来控制输入内容类型，包括：
- text
- multiline（多行文本）
- number，还可以详细设置为 signed 或 decimal
- phone
- datetime
- emailAddress
- url

> 需要注意的是，`TextInputType`是配合 `TextFiled`的`keyboardType`使用的，键盘显示是对应输入类型的。
```dart
//  输入框输入类型，键盘显示类型
keyboardType: TextInputType.phone,
```

#### controller
这里使用的是 TextEditingController，一般用于获取输入框值、输入框清空、选择、监听等操作。
```dart
// 输入框的控制器，监听器
TextEditingController controller
```
实例：
```dart
// 通过controller监听 TextField 输入值的变化。 同 onChanged
TextEditingController _controller;
@override
void initState(){
  super.initState();
  // 可用例_controller 操作当前TextField
  _controller = TextEditingController();
  // 在任何时候想获取输入框文本内容就直接调用：_controller.text
  //设置默认值
  _controller.text="我在学习Flutter!";
  // 监听变化
  _controller.addListener((){
    String _text = _controller.text;  // TextField 当前值
    print('$_text');
  });
}

 // onChanged, text是当前输入框的值
_handleChange(text){
  print(text);
}
```

#### focusNode
focusNode 用于控制输入框焦点，如我们点击回车时候，让某个输入框获取焦点等类似操作或者监听焦点变化。

```dart
FocusNode focusNode1 = new FocusNode();
FocusScopeNode focusScopeNode;
...
// 实例化FocusScopeNode
focusScopeNode = FocusScope.of(context);
...
TextField(
    focusNode: focusNode1,
),
...
// 操作焦点
focusScopeNode.requestFocus(focusNode1);
focusScopeNode.autofocus(focusNode1);
```

#### TextField 实例

```dart
TextField(
  // 输入文本样式
  style: TextStyle(color: Colors.blueAccent),
  maxLength: 11,
  // 输入类型
  keyboardType: TextInputType.phone,
  // 设置样式
  decoration: const InputDecoration(
    //左侧显示的图标
    icon: Icon(Icons.phone),
    // 输入框顶部提示信息
    labelText: '输入手机号',
    labelStyle: TextStyle(color: Colors.grey),
    helperText: '提示：11位手机号码',
    border: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.grey),
      borderRadius: BorderRadius.all(Radius.circular(5.0))
    ),
    focusedBorder: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.red)
    )
  ),
)
```
{% img /images/2019-08-16/flutter_study_02.png %}
{% img /images/2019-08-16/flutter_study_01.png 'TextField Widget Sample'%}



### Form 表单
> Form 继承自 StatefulWidget，Form 的里面每一个子元素必须是 FormField 类型。就像我们表单里每一条信息都要用 FormField 包装和管理，所以一般 Form 和 `TextFormField` 搭配使用。

#### 构造方法

##### Form
```dart
const Form({
  Key key,
  // 子元素。 必须项
  @required Widget child,
  // 是否自动校验子元素输入的内容
  bool autovalidate: false,
  // 返回按键处理
  WillPopCallback onWillPop,
  // Form的任意一个子FormField内容发生变化时触发此回调
  VoidCallback onChanged
})
```

##### FormField
```dart
const FormField<T>({
  Key key,
  // 必需项
  @required FormFieldBuilder<T> builder,
  // 保存回调
  FormFieldSetter<T> onSaved,
  // 验证回调
  FormFieldValidator<T> validator,
  // 初始值
  T initialValue,
  // 是否自动校验
  bool autovalidate: false,
  // 是否可用
  bool enabled: true
})
```

##### TextFormField
TextFormField 构造方法，继承自 FormField：
```dart
TextFormField({
    Key key,
    this.controller,
    String initialValue,
    FocusNode focusNode,
    InputDecoration decoration = const InputDecoration(),
    TextInputType keyboardType,
    TextCapitalization textCapitalization = TextCapitalization.none,
    TextInputAction textInputAction,
    TextStyle style,
    TextDirection textDirection,
    TextAlign textAlign = TextAlign.start,
    bool autofocus = false,
    bool obscureText = false,
    bool autocorrect = true,
    bool autovalidate = false,
    bool maxLengthEnforced = true,
    int maxLines = 1,
    int maxLength,
    VoidCallback onEditingComplete,
    // 提交数据
    ValueChanged<String> onFieldSubmitted,
    FormFieldSetter<String> onSaved,
    FormFieldValidator<String> validator,
    List<TextInputFormatter> inputFormatters,
    bool enabled = true,
    double cursorWidth = 2.0,
    Radius cursorRadius,
    Color cursorColor,
    Brightness keyboardAppearance,
    EdgeInsets scrollPadding = const EdgeInsets.all(20.0),
    bool enableInteractiveSelection = true,
    InputCounterWidgetBuilder buildCounter,
  })
```

#### 通信
Form 和 FormField 是如何管理和通信的呢？答案就是通过 key 和 `FormState`

```dart
GlobalKey<FormState> _formKey = new GlobalKey<FormState>();
FormState _formState;
... 

_formState = _formKey.currentState;

Form(
    key: _formKey, // 通过Form 的 key ，绑定关系
... 
// 然后通过调用FormState相关方法，就可以它来对Form的子元素FormField进行统一操作。例如：
// 保存
_formState.save();
// 清空重置
_formState.reset();
// 验证
_formState.validate();

// 调用这几个方法后，会调用Form子元素FormField的对应的方法回调，这样就达到了控制和管理操作
```
{% img /images/2019-08-16/flutter_study_03.png 'Form Widget Sample'%}


```dart
// 关键代码
GlobalKey<FormState> _formKey = new GlobalKey<FormState>();
  FormState _formState;
  String _name;
  String _password;

  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Form Widget'),
        backgroundColor: Colors.deepPurpleAccent,
      ),
      body: Form(
        key: _formKey,
        child: Column(
          children: <Widget>[
            // TextFormField用于表单
            TextFormField(
              decoration: const InputDecoration(
                icon: Icon(Icons.person),
                hintText: '请输入用户名', // placeholder
                labelText: '用户名',
                prefixText: '用户名：'
              ),
              validator: (value){
                if(value.length == 0){
                  return '请输入用户名';
                }
                return value.contains('@') ? '不能包含@符号' : null;
              },
              onSaved: (value) {
                _name = value;
              },
            ),

            TextFormField(
              obscureText: true,
              maxLength: 12,
              maxLines: 1,
              keyboardType: TextInputType.numberWithOptions(),
              decoration: InputDecoration(
                icon: Icon(Icons.lock),
                hintText: '请输入密码',
                labelText: '密码',
                prefixText: '密码：'
              ),
              validator: (String value) {
                return value.trim().length < 10 ? '密码长度为10~12位' : null;
              },
              onSaved: (value) {
                _password = value;
              },
            ),

            RaisedButton(
              child: Text('登录'),
              color: Colors.deepPurpleAccent,
              textColor: Colors.white,
              onPressed: (){
                onSubmit();
              },
            )
          ],
        ),
      ),
    );
  }

  void onSubmit(){
    _formState = _formKey.currentState;
    if(_formState.validate()){
      _formState.save();
      print('$_name');
      print('$_password');
      showDialog<void>(
        context: context,
        barrierDismissible: false,
        builder: (BuildContext context){
          return AlertDialog(
            title: Text('登录成功'),
            content: Column(
              children: <Widget>[
                Text('用户名：$_name'),
                Text('密码：$_password'),
              ],
            ),
            actions: <Widget>[
              RaisedButton(
                textColor: Colors.white, 
                color: Colors.deepPurpleAccent,
                child: Text('确定'),
                onPressed: (){
                  Navigator.of(context).pop(true);
                },
              )
            ],
          );
        }
      );
    }
  }
```

---
[相关代码](https://github.com/SeptemberMaples/study-section/tree/flutter_study/flutter_study/lib/flutter_04)


---
参考：

- [flutter官网](https://api.flutter.dev/flutter/material/TextField/TextField.html)

-【Flutter—从入门到实践(by Ray)】学习笔记
