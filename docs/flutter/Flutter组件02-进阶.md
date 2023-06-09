## dialog

* `AlertDialog`：对话框
* `SimpleDialog`：列表选择对话框
* `showModalBottomSheet`：底部菜单对话框
* `showLoadingDialog`：loading对话框
* `showDatePicker`：日历选择对话框

```dart
const AlertDialog({
  Key? key,
  this.title, //对话框标题组件
  this.titlePadding, // 标题填充
  this.titleTextStyle, //标题文本样式
  this.content, // 对话框内容组件
  this.contentPadding = const EdgeInsets.fromLTRB(24.0, 20.0, 24.0, 24.0), //内容的填充
  this.contentTextStyle,// 内容文本样式
  this.actions, // 对话框操作按钮组
  this.backgroundColor, // 对话框背景色
  this.elevation,// 对话框的阴影
  this.semanticLabel, //对话框语义化标签(用于读屏软件)
  this.shape, // 对话框外形
})
```

eg:

```dart
AlertDialog(
  title: Text("提示"),
  content: Text("您确定要删除当前文件吗?"),
  actions: <Widget>[
    TextButton(
      child: Text("取消"),
      onPressed: () => Navigator.of(context).pop(), //关闭对话框
    ),
    TextButton(
      child: Text("删除"),
      onPressed: () {
        // ... 执行删除操作
        Navigator.of(context).pop(true); //关闭对话框
      },
    ),
  ],
);
```

> 弹出dialog

```dart
//点击该按钮后弹出对话框
RaisedButton(
  child: Text("对话框1"),
  onPressed: () async {
    //弹出对话框并等待其关闭
    bool? delete = await showDeleteConfirmDialog1();
    if (delete == null) {
      print("取消删除");
    } else {
      print("已确认删除");
      //... 删除文件
    }
  },
),

// 弹出对话框
Future<bool> showDeleteConfirmDialog1() {
  return showDialog<bool>(
    context: context,
    builder: (context) {
      return AlertDialog(
        //...
      );
    },
  );
}
```

## 触摸事件

### 原始事件处理

```dart
Listener({
  Key key,
  this.onPointerDown, //手指按下回调
  this.onPointerMove, //手指移动回调
  this.onPointerUp,//手指抬起回调
  this.onPointerCancel,//触摸事件取消回调
  this.behavior = HitTestBehavior.deferToChild, //先忽略此参数，后面小节会专门介绍
  Widget child
})
```

`PointerEvent`:

- `position`：它是指针相对于当对于全局坐标的偏移
- `localPosition`: 它是指针相对于当对于全局坐标的偏移
- `delta`：两次指针移动事件（`PointerMoveEvent`）的距离
- `orientation`：指针移动方向，是一个角度值

### 手势识别

`GestureDetector`是一个用于手势识别的功能性组件，我们通过它可以来识别各种手势。`GestureDetector` 内部封装了 Listener，用以识别语义化的手势

* `onTap`：单击
* `onDoubleTap`：双击
* `onLongPress`：长按
* `onPanDown`：按下
* `onPanUpdate`：滑动
* `onPanEnd`：抬起
* `onVerticalDragUpdate`：垂直方向拖动
* `onScaleUpdate`：缩放

`GestureRecognizer???`
