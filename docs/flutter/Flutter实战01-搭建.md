在`main`函数首行调用`ensureInitialized`确保库已经初始化

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
 }
```

设置状态栏透明

```dart
//设置状态栏为透明
SystemUiOverlayStyle systemUiOverlayStyle = const SystemUiOverlayStyle(
  statusBarColor: Colors.transparent,
  statusBarIconBrightness: Brightness.dark,
);
SystemChrome.setSystemUIOverlayStyle(systemUiOverlayStyle);
```

可以使用`runZonedGuarded`捕获全局异常

```dart
void main() async {
    runZonedGuarded(
      () {
        runApp(const MyApp());
      },
      (error, stackTrace) {
        //全局异常
        Log.e(error.toString(), stackTrace);
      },
    );
}
```

屏蔽左侧返回按钮

```
automaticallyImplyLeading: false
```