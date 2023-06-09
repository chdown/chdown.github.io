## Widget

`Flutter`中万物皆`Widght`，`Widget`描述一个`UI`元素的配置信息(接收的参数，例如文本的内容、对齐方式)，而`Widget`被创建后，它的属性和状态就不能再被修改，因此**Widget是不可变的**。

为了解决上面的问题，系统提供了`StatelessWidge（无状态）`和 `StateFulWidget(有状态)`，它们之间的差别在于是否能重建：

`StatelessWidget`用于不需要维护状态的场景，它通常在`build`方法中通过嵌套其他`widget`来构建`UI`

`StateFulWidget`用于需要维护状态的场景，而一个`StatefulWidget`类会对应一个`State`类,`State`更新方式：

* 通过`state.setState()`触发重布局。
* 遍历需要更新的`Element`，依次调用`rebuild

`StateFulWidget`的生命周期：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/339ca501fa0e4f9a8883fd3ba0ec72b0~tplv-k3u1fbpfcp-watermark.image?)

> tip：`widget`本质上一个树形结构，因此`Widget`树一旦不可见就会被移除

## 在 widget 树中获取State对象

`context`对象有一个`findAncestorStateOfType()`方法，该方法可以从当前节点沿着`widget`树向上查找指定类型的`StatefulWidget`对应的`State`对象。

```dart
 // 查找父级最近的Scaffold对应的ScaffoldState对象
ScaffoldState _state = context.findAncestorStateOfType<ScaffoldState>()!;
// 打开抽屉菜单
_state.openDrawer();
```

**通常`StatefulWidget`的状态是私有的**，如果希望暴露出状态，应当提供一个`of`静态方法获取`State`对象。

```dart
// 直接通过of静态方法来获取ScaffoldState
ScaffoldState _state=Scaffold.of(context);
// 打开抽屉菜单
_state.openDrawer();

ScaffoldMessenger.of(context).showSnackBar(
	SnackBar(content: Text("我是SnackBar")),
);
```

通过`GlobalKey`来获取，同一个`GlobalKey`在整个`widget`树中必须是唯一，而且**开销较大，避免使用**。

```dart
/定义一个globalKey, 由于GlobalKey要保持全局唯一性，我们使用静态变量存储
static GlobalKey<ScaffoldState> _globalKey= GlobalKey();
Scaffold(
    key: _globalKey , //设置key
)
    
_globalKey.currentState.openDrawer()
```

## State状态管理

* 管理自身：通过`setState()`方法更新`State`

* 父管理子：通过方法回调管理

  ```dart
  class ParentWidget extends StatefulWidget {
    @override
    _ParentWidgetState createState() => _ParentWidgetState();
  }
  
  class _ParentWidgetState extends State<ParentWidget> {
    bool _active = false;
  
    void _handleTapboxChanged(bool newValue) {
      setState(() {
        _active = newValue;
      });
    }
  
    @override
    Widget build(BuildContext context) {
      return Container(
        child: TapboxB(
          active: _active,
          onChanged: _handleTapboxChanged,
        ),
      );
    }
  }
  
  
  class TapboxB extends StatelessWidget {
    TapboxB({Key? key, this.active: false, required this.onChanged}) : super(key: key);
  
    final bool active;
    final ValueChanged<bool> onChanged;
  
    void _handleTap() {
      onChanged(!active);
    }
  
    Widget build(BuildContext context) {
      return GestureDetector(
        onTap: _handleTap,
        child: ...;
    }
  }
  ```

* 混合管理：以上两种方式都存在，即组件自身管理一些内部状态，而父组件管理一些其他外部状态

* 全局状态管理：如语言等发生变化，通过使用一些专门用于状态管理的包，如 Provider、Redux

## 路由管理

### Navigator

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ef1bd36e1d14182ac7e194ed9c70b26~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

```dart
class TipRoute extends StatelessWidget {
  TipRoute({
    Key key,
    required this.text,  // 接收一个text参数
  }) : super(key: key);
  final String text;

  @override
  Widget build(BuildContext context) {
  	//.....
  }
}

// 传参
Navigator.push( 
	context,
	MaterialPageRoute(builder: (context) {
		return TipRoute("tip");
	}),
);
```

### 命名路由

```dart
MaterialApp(
  title: 'Flutter Demo',
  initialRoute:"/", //名为"/"的路由作为应用的home(首页)
  theme: ThemeData(
    primarySwatch: Colors.blue,
  ),
  //注册路由表
  routes:{
   "new_page":(context) => NewRoute(),
   "/":(context) => MyHomePage(title: 'Flutter Demo Home Page'), //注册首页路由
  } 
);
Navigator.pushNamed(context, "new_page");

// 传参
Navigator.of(context).pushNamed("new_page", arguments: "hi");
//获取路由参数  
var args=ModalRoute.of(context).settings.arguments;
```

### 路由钩子、拦截器

`onGenerateRoute` 只会对命名路由生效

```dart
MaterialApp(
  ... //省略无关代码
  onGenerateRoute:(RouteSettings settings){
	  return MaterialPageRoute(builder: (context){
		   String routeName = settings.name;
       // 如果访问的路由页需要登录，但当前未登录，则直接返回登录页路由，
       // 引导用户登录；其他情况则正常打开路由。
     }
   );
  }
);
```

## 包依赖

### Pub仓库

访问[Pub](https://pub.dev/)库，查找需要的依赖列表

手动添加至`pubspec.yaml`中，单击右上角的 **Pub get** 。

```yaml
dependencies:
  flutter:
    sdk: flutter
  # 新添加的依赖
  english_words: ^4.0.0
```

### 本地包

```yaml
dependencies:
	pkg1:
        path: ../../code/pkg1
```

### Git包

```yaml
dependencies:
  pkg1:
    git:
      url: git://github.com/xxx/pkg1.git
  package1:
    git:
      url: git://github.com/flutter/packages.git
      path: packages/package1        
```

## 资源管理

`assets`（资源）是指我们常用的静态数据（例如JSON文件）、配置文件、图标和图片等。

```yaml
flutter:
  assets:
    - image/
```

### 加载文本assets

```dart
import 'dart:async' show Future;
import 'package:flutter/services.dart' show rootBundle;

Future<String> loadAsset() async {
  return await rootBundle.loadString('assets/config.json');
}
DefaultAssetBundle.of().loadString('assets/config.json');
```

### 加载图片

```dart
AssetImage('graphics/background.png'),
Image.asset('graphics/background.png'),
AssetImage('icons/heart.png', package: 'my_icons'),
Image.asset('icons/heart.png', package: 'my_icons'),
```

## 返回拦截

Flutter中可以通过`WillPopScope`来实现返回按钮拦截

```dart
const WillPopScope({
  ...
  required WillPopCallback onWillPop,
  requi
```

`onWillPop`是一个回调函数，当用户点击返回按钮时被调用（包括导航返回按钮及Android物理返回按钮）。该回调需要返回一个`Future`对象，如果返回的`Future`最终值为`false`时，则当前路由不出栈(不会返回)；最终值为`true`时，当前路由出栈退出。我们需要提供这个回调来决定是否退出。

```dart
import 'package:flutter/material.dart';

class WillPopScopeTestRoute extends StatefulWidget {
  @override
  WillPopScopeTestRouteState createState() {
    return WillPopScopeTestRouteState();
  }
}

class WillPopScopeTestRouteState extends State<WillPopScopeTestRoute> {
  DateTime? _lastPressedAt; //上次点击时间

  @override
  Widget build(BuildContext context) {
    return WillPopScope(
      onWillPop: () async {
        if (_lastPressedAt == null ||
            DateTime.now().difference(_lastPressedAt!) > Duration(seconds: 1)) {
          //两次点击间隔超过1秒则重新计时
          _lastPressedAt = DateTime.now();
          return false;
        }
        return true;
      },
      child: Container(
        alignment: Alignment.center,
        child: Text("1秒内连续按两次返回键退出"),
      ),
    );
  }
}
```

## 异步UI更新

### FutureBuilder

通过`Future`的状态来动态构建自身，下面的代码展示了打开页面后从网络获取数据，数据获取成功后进行展示。

```dart
Future<String> mockNetworkData() async {
  return Future.delayed(Duration(seconds: 2), () => "我是从互联网上获取的数据");
}

...
Widget build(BuildContext context) {
  return Center(
    child: FutureBuilder<String>(
      future: mockNetworkData(),
      builder: (BuildContext context, AsyncSnapshot snapshot) {
        // 请求已结束
        if (snapshot.connectionState == ConnectionState.done) {
          if (snapshot.hasError) {
            // 请求失败，显示错误
            return Text("Error: ${snapshot.error}");
          } else {
            // 请求成功，显示数据
            return Text("Contents: ${snapshot.data}");
          }
        } else {
          // 请求未结束，显示loading
          return CircularProgressIndicator();
        }
      },
    ),
  );
}
```

### StreamBuilder

可以接收多个异步操作的结果，它常用于会多次读取数据的异步任务场景，如网络内容下载、文件读写等。`StreamBuilder`正是用于配合`Stream`来展示流上事件（数据）变化的UI组件

eg：我们创建一个计时器的示例：每隔1秒，计数加1。这里，我们使用`Stream`来实现每隔一秒生成一个数字:

```dart
Stream<int> counter() {
  return Stream.periodic(Duration(seconds: 1), (i) {
    return i;
  });
}
```

`StreamBuilder`使用代码如下：

```dart
 Widget build(BuildContext context) {
    return StreamBuilder<int>(
      stream: counter(), //
      //initialData: ,// a Stream<int> or null
      builder: (BuildContext context, AsyncSnapshot<int> snapshot) {
        if (snapshot.hasError)
          return Text('Error: ${snapshot.error}');
        switch (snapshot.connectionState) {
          case ConnectionState.none:
            return Text('没有Stream');
          case ConnectionState.waiting:
            return Text('等待数据...');
          case ConnectionState.active:
            return Text('active: ${snapshot.data}');
          case ConnectionState.done:
            return Text('Stream 已关闭');
        }
        return null; // unreachable
      },
    );
 }
```

## 前后台监听

`WidgetsBindingObserver`包含了各种控件的生命周期通知，其中的`didChangeAppLifecycleState`就可以做前后台状态监听。

```dart
/// WidgetsBindingObserver 包含了各种控件的生命周期通知
class _HomePageState extends State<HomePage> with WidgetsBindingObserver {

  ///重写 WidgetsBindingObserver 中的 didChangeAppLifecycleState
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    ///通过state判断App前后台切换
    if (state == AppLifecycleState.resumed) {

    }
  }

  @override
  Widget build(BuildContext context) {
    return new Container();
  }
}
```
