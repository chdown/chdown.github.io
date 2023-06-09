## Context-State问题

当我们在`dialog`的`children`中加入了一个`checkBox`，但是我们点击后无法进行选择，这是因为`setState`方法只会针对当前`context`的子树重新`build`,可以通过以下几种方式处理：

* 单独抽出`dialog`

* 通过`StatefulBuilder`构建

  ```dart
  StatefulBuilder(
    builder: (context, _setState) {
      return Checkbox(
        value: _withTree, //默认不选中
        onChanged: (bool value) {
          //_setState方法实际就是该StatefulWidget的setState方法，
          //调用后builder方法会重新被调用
          _setState(() {
            //更新选中状态
             _withTree = !_withTree;
          });
         },
       );
     },
  ),
  ```

* `markNeedsBuild`:`setState`中调用了`Element`的`markNeedsBuild()`方法。

  ```dart
  checkbox( // 依然使用Checkbox组件
    value: _withTree,
    onChanged: (bool value) {
      // 此时context为对话框UI的根Element，我们直接将对话框UI对应的Element标记为dirty
      (context as Element).markNeedsBuild();
      _withTree = !_withTree;
    },
  ),
  ```

  再次优化：缩小context范围

  ```dart
  Builder(
    builder: (BuildContext context) {
      checkbox( // 依然使用Checkbox组件
        value: _withTree,
        onChanged: (bool value) {
          // 通过Builder来获得构建Checkbox的`context`
          (context as Element).markNeedsBuild();
          _withTree = !_withTree;
        },
    },
  ),
  ```

## 关闭键盘

```dart
FocusScope.of(context).requestFocus(FocusNode());
```

## 配置信息

`MediaQuery.of(context).size`：获取屏幕大小

`MediaQueryData.fromWindow(WidgetsBinding.instance.window).padding.top`：获取状态栏高度
