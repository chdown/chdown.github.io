# 一、基础组件

## 1、文本

### Text

```dart
Text("Hello world",
  style: TextStyle(
    color: Colors.blue,
    fontSize: 18.0,
    height: 1.2,  
    fontFamily: "Courier",
    background: Paint()..color=Colors.yellow,
    decoration:TextDecoration.underline,
    decorationStyle: TextDecorationStyle.dashed
  ),
);
```

### TextSpan

```dart
TapGestureRecognizer _tapGestureRecognizer = TapGestureRecognizer();

@override
void dispose() {
    //用到GestureRecognizer的话一定要调用其dispose方法释放资源
    _tapGestureRecognizer.dispose();
    super.dispose();
}

Text.rich(TextSpan(
    children: [
     TextSpan(
       text: "Home: "
     ),
     TextSpan(
       text: "https://flutterchina.club",
       style: TextStyle(
         color: Colors.blue
       ),  
       recognizer: _tapRecognizer
         ..onTap = () {
                  setState(() {
                    _toggle = !_toggle;
                  });
             },
     ),
    ]
))
```

### DefaultTextStyle

在 Widget 树中，文本的样式默认是可以被继承的，`DefaultTextStyle`正是用于设置默认文本样式，因此该节点的子树中所有文本都会默认使用这个样式。

```dart
DefaultTextStyle(
  //1.设置文本默认样式  
  style: TextStyle(
    color:Colors.red,
    fontSize: 20.0,
  ),
  textAlign: TextAlign.start,
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    children: <Widget>[
      Text("hello world"),
      Text("I am Jack"),
      Text("I am Jack",
        style: TextStyle(
          inherit: false, //2.不继承默认样式
          color: Colors.grey
        ),
      ),
    ],
  ),
);
```

## 2、输入组件

### TextField

```
TextField(
  autofocus: true,
  decoration: InputDecoration(
    labelText: "用户名",
    hintText: "用户名或邮箱",
    prefixIcon: Icon(Icons.person)
  ),
)
```

- `controller`：编辑框的控制器，通过它可以设置/获取编辑框的内容、选择编辑内容、监听编辑文本改变事件。大多数情况下我们都需要显式提供一个`controller`来与文本框交互。如果没有提供`controller`，则`TextField`内部会自动创建一个。
- `focusNode`：用于控制`TextField`是否占有当前键盘的输入焦点。它是我们和键盘交互的一个句柄（handle）。
- `InputDecoration`：用于控制`TextField`的外观显示，如提示文本、背景颜色、边框等。
- `keyboardType`：用于设置该输入框默认的键盘输入类型
- `textInputAction`：键盘动作按钮图标(即回车键位图标)
- `style`：正在编辑的文本样式。
- `textAlign`: 输入框内编辑文本在水平方向的对齐方式。
- `autofocus`: 是否自动获取焦点。
- `obscureText`：是否隐藏正在编辑的文本，如用于输入密码的场景等，文本内容会用“•”替换。
- `maxLines`：输入框的最大行数，默认为1；如果为`null`，则无行数限制。
- `maxLength`和`maxLengthEnforcement` ：`maxLength`代表输入框文本的最大长度，设置后输入框右下角会显示输入的文本计数。`maxLengthEnforcement`决定当输入文本长度超过`maxLength`时如何处理，如截断、超出等。
- `toolbarOptions`：长按或鼠标右击时出现的菜单，包括 copy、cut、paste 以及 selectAll。
- `onChange`：输入框内容改变时的回调函数；注：内容改变事件也可以通过`controller`来监听。
- `onEditingComplete`和`onSubmitted`：这两个回调都是在输入框输入完成时触发，比如按了键盘的完成键（对号图标）或搜索键（🔍图标）。不同的是两个回调签名不同，`onSubmitted`回调是`ValueChanged<String>`类型，它接收当前输入内容做为参数，而`onEditingComplete`不接收参数。
- `inputFormatters`：用于指定输入格式；当用户输入内容改变时，会根据指定的格式来校验。
- `enable`：如果为`false`，则输入框会被禁用，禁用状态不接收输入和事件，同时显示禁用态样式（在其`decoration`中定义）。
- `cursorWidth`、`cursorRadius`和`cursorColor`：这三个属性是用于自定义输入框光标宽度、圆角和颜色的。

> 获取内容

1. 定义两个变量，用于保存用户名和密码，然后在`onChange`触发时，各自保存一下输入内容。
2. 通过`controller`直接获取。

> 监听文本变化

```dart
TextField(
    autofocus: true,
    onChanged: (v) {
      print("onChange: $v");
    }
)
```

```dart
@override
void initState() {
  //监听输入改变  
  _unameController.addListener((){
    print(_unameController.text);
  });
}
```

### Form

```dart
Form({
  required Widget child,
  bool autovalidate = false,
  WillPopCallback onWillPop,
  VoidCallback onChanged,
})
```

- `autovalidate`：是否自动校验输入内容；当为`true`时，每一个子 FormField 内容发生变化时都会自动校验合法性，并直接显示错误信息。否则，需要通过调用`FormState.validate()`来手动校验。
- `onWillPop`：决定`Form`所在的路由是否可以直接返回（如点击返回按钮），该回调返回一个`Future`对象，如果 Future 的最终结果是`false`，则当前路由不会返回；如果为`true`，则会返回到上一个路由。此属性通常用于拦截返回按钮。
- `onChanged`：`Form`的任意一个子`FormField`内容发生变化时会触发此回调。

#### FormField

```dart
const FormField({
  ...
  FormFieldSetter<T> onSaved, //保存回调
  FormFieldValidator<T>  validator, //验证回调
  T initialValue, //初始值
  bool autovalidate = false, //是否自动校验。
})
```

#### TextFormField

继承自`FormField`类，也是`TextField`的一个包装类

```dart
TextFormField(
  autofocus: true,
  controller: _unameController,
  decoration: InputDecoration(
    labelText: "用户名",
    hintText: "用户名或邮箱",
    icon: Icon(Icons.person),
  ),
  // 校验用户名
  validator: (v) {
    return v!.trim().length > 0 ? null : "用户名不能为空";
  },
)
```

#### FormState

`FormState`为`Form`的`State`类，可以通过`Form.of()`或`GlobalKey`获得。我们可以通过它来对`Form`的子孙`FormField`进行统一操作。我们看看其常用的三个方法：

- `FormState.validate()`：调用此方法后，会调用`Form`子孙`FormField的validate`回调，如果有一个校验失败，则返回false，所有校验失败项都会返回用户返回的错误提示。
- `FormState.save()`：调用此方法后，会调用`Form`子孙`FormField`的`save`回调，用于保存表单内容
- `FormState.reset()`：调用此方法后，会将子孙`FormField`的内容清空。

> 数据提交

```dart
Form(
  key: _formKey, //设置globalKey，用于后面获取FormState
  autovalidateMode: AutovalidateMode.onUserInteraction,
  //....
}
    
// 提交代码
// 通过_formKey.currentState 获取FormState后，调用validate()方法校验是否合法，校验通过后再提交数据。
if ((_formKey.currentState as FormState).validate()) {
	// 验证通过提交数据
}
```

## 3、按钮

### ElevatedButton

默认带有阴影和灰色背景

```dart
ElevatedButton(
  child: Text("normal"),
  label: Text("发送"),
  onPressed: () {},
);
```

### TextButton

文本按钮

```dart
TextButton(
  child: Text("normal"),
  label: Text("发送"),
  onPressed: () {},
)
```

### OutlineButton

默认有一个边框，不带阴影且背景透明

```dart
OutlineButton(
  child: Text("normal"),
  label: Text("发送"),
  onPressed: () {},
)
```

### IconButton

不包括文字，默认没有背景，点击后会出现背景

```dart
IconButton(
  icon: Icon(Icons.thumb_up),
  label: Text("发送"),
  onPressed: () {},
)
```

## 4、图片和ICON

### Image

`Image`组件来加载并显示图片，`Image`的数据源可以是asset、文件、内存以及网络。

```dart
Image(
  image: AssetImage("images/avatar.png"),
  width: 100.0
);
Image.asset("images/avatar.png", width: 100.0)
```

```dart
Image(
  image: NetworkImage("https://avatars2.githubusercontent.com/u/20411648?s=460&v=4"),
  width: 100.0,
)
Image.network("https://avatars2.githubusercontent.com/u/20411648?s=460&v=4", width: 100.0)
```

`fit`：适应模式：

- `fill`：会拉伸填充满显示空间，图片本身长宽比会发生变化，图片会变形。
- `cover`：会按图片的长宽比放大后居中填满显示空间，图片不会变形，超出显示空间部分会被剪裁。
- `contain`：这是图片的默认适应规则，图片会在保证图片本身长宽比不变的情况下缩放以适应当前显示空间，图片不会变形。
- `fitWidth`：图片的宽度会缩放到显示空间的宽度，高度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
- `fitHeight`：图片的高度会缩放到显示空间的高度，宽度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
- `none`：图片没有适应策略，会在显示空间内显示图片，如果图片比显示空间大，则显示空间只会显示图片中间部分。

### ICON

> **字体图标**

1. 修改配置

   ```yaml
   flutter:
     uses-material-design: true
   ```

2. 默认支持的图标[material icon]([https://material.io/tools/icons/))

   ```dart
   String icons = "\uE03e";
   
   Text(
     icons,
     style: TextStyle(
       fontFamily: "MaterialIcons",
       fontSize: 24.0,
       color: Colors.green,
     ),
   );
   ```

3. **使用系统提供的字体图标**

   ```dart
   Row(
     mainAxisAlignment: MainAxisAlignment.center,
     children: <Widget>[
       Icon(Icons.accessible,color: Colors.green),
       Icon(Icons.error,color: Colors.green),
       Icon(Icons.fingerprint,color: Colors.green),
     ],
   )
   ```

> **自定义字体图标**

1. 添加配置

   ```yaml
   fonts:
     - family: myIcon  #指定一个字体名
       fonts:
         - asset: fonts/iconfont.ttf
   ```

2. 定义一个`MyIcons`类，功能和`Icons`类一样：将字体文件中的所有图标都定义成静态变量：

   ```dart
   class MyIcons{
     // book 图标
     static const IconData book = const IconData(
         0xe614, 
         fontFamily: 'myIcon', 
         matchTextDirection: true
     );
     // 微信图标
     static const IconData wechat = const IconData(
         0xec7d,  
         fontFamily: 'myIcon', 
         matchTextDirection: true
     );
   }
   ```

3. 使用

   ```dart
   Row(
     mainAxisAlignment: MainAxisAlignment.center,
     children: <Widget>[
       Icon(MyIcons.book,color: Colors.purple),
       Icon(MyIcons.wechat,color: Colors.green),
     ],
   )
   ```

## 5、开关

`Switch`和复选框`Checkbox`本身不会保存当前选中状态，选中状态都是由父组件来管理的，因此我们可以在此回调中处理选中状态改变逻辑。

### Switch

```dart
bool _switchSelected=true; //维护单选开关状态

Switch(
	value: _switchSelected,
	onChanged:(value){
	},	
)
```

### Checkbox

```dart
bool _checkboxSelected=true; //维护单选开关状态

Checkbox(
	value: _checkboxSelected,
	onChanged:(value){
	},	
)
```

## 6、进度条

### LinearProgressIndicator

```dart
LinearProgressIndicator({
  double value,
  Color backgroundColor,
  Animation<Color> valueColor,
  ...
})
```

### CircularProgressIndicator

```dart
 CircularProgressIndicator({
  double value,
  Color backgroundColor,
  Animation<Color> valueColor,
  this.strokeWidth = 4.0,
  ...   
}) 
```

# 二、容器组件

## 1、Container

`Container`是一个组合类容器，它本身不对应具体的`RenderObject`，它是`DecoratedBox`、`ConstrainedBox、Transform`、`Padding`、`Align`等组件组合的一个多功能容器，所以我们只需通过一个`Container`组件可以实现同时需要装饰、变换、限制的场景。

如果要添加 padding、margin、边框或背景颜色，你就可以使用 `Container` 

```dart
Container({
  this.alignment,
  this.padding, //容器内补白，属于decoration的装饰范围
  Color color, // 背景色
  Decoration decoration, // 背景装饰
  Decoration foregroundDecoration, //前景装饰
  double width,//容器的宽度
  double height, //容器的高度
  BoxConstraints constraints, //容器大小的限制条件
  this.margin,//容器外补白，不属于decoration的装饰范围
  this.transform, //变换
  this.child,
  ...
})
```

## 2、Padding

`Padding`可以给其子节点添加填充（留白），和边距效果类似

```dart
Padding({
  ...
  EdgeInsetsGeometry padding,
  Widget child,
})
```

## 3、DecoratedBox

`DecoratedBox`可以在其子组件绘制前(或后)绘制一些装饰（Decoration），如背景、边框、渐变等。`DecoratedBox`定义如下：

```dart
const DecoratedBox({
  Decoration decoration,
  DecorationPosition position = DecorationPosition.background,
  Widget? child
})
```

我们通常会直接使用`BoxDecoration`类，它是一个Decoration的子类，实现了常用的装饰元素的绘制。

```dart
BoxDecoration({
  Color color, //颜色
  DecorationImage image,//图片
  BoxBorder border, //边框
  BorderRadiusGeometry borderRadius, //圆角
  List<BoxShadow> boxShadow, //阴影,可以指定多个
  Gradient gradient, //渐变
  BlendMode backgroundBlendMode, //背景混合模式
  BoxShape shape = BoxShape.rectangle, //形状
})
```

## 4、Transform

`Transform`可以在其子组件绘制时对其应用一些矩阵变换来实现一些特效。`Matrix4`是一个4D矩阵，通过它我们可以实现各种矩阵操作

* `Transform.translate`
* `Transform.rotate`
* `Transform.scale`

## 5、Clip

| 剪裁Widget | 默认行为                                                 |
| ---------- | -------------------------------------------------------- |
| ClipOval   | 子组件为正方形时剪裁成内贴圆形；为矩形时，剪裁成内贴椭圆 |
| ClipRRect  | 将子组件剪裁为圆角矩形                                   |
| ClipRect   | 默认剪裁掉子组件布局空间之外的绘制内容（溢出部分剪裁）   |
| ClipPath   | 按照自定义的路径剪裁                                     |



# 三、布局组件

## 1、*布局约束

### BoxConstraints

BoxConstraints 是盒模型布局过程中父渲染对象传递给子渲染对象的约束信息，包含最大宽高信息，子组件大小需要在约束的范围内

```dart
const BoxConstraints({
  this.minWidth = 0.0, //最小宽度
  this.maxWidth = double.infinity, //最大宽度
  this.minHeight = 0.0, //最小高度
  this.maxHeight = double.infinity //最大高度
})
```

`BoxConstraints.tight(Size size)`，它可以生成固定宽高的限制

`BoxConstraints.expand()`可以生成一个尽可能大的用以填充另一个容器的BoxConstraints

### ConstrainedBox

`ConstrainedBox`用于对子组件添加额外的约束。例如，如果你想让子组件的最小高度是80像素，你可以使用`const BoxConstraints(minHeight: 80.0)`作为子组件的约束。

```dart
Widget redBox = DecoratedBox(
  decoration: BoxDecoration(color: Colors.red),
);
// 下面的代码中，虽然Container高度虽然为5像素，但是最终却是50像素，这正是ConstrainedBox的最小高度限制生效了
ConstrainedBox(
  constraints: BoxConstraints(
    minWidth: double.infinity, //宽度尽可能大
    minHeight: 50.0 //最小高度为50像素
  ),
  child: Container(
    height: 5.0, 
    child: redBox ,
  ),
)
```

### SizedBox

`SizedBox`用于给子元素指定固定的宽高

```dart
SizedBox(
  width: 80.0,
  height: 80.0,
  child: redBox
)
```

### UnconstrainedBox

`UnconstrainedBox` 的子组件将不再受到约束，大小完全取决于自己

```dart
ConstrainedBox(
  constraints: BoxConstraints(minWidth: 60.0, minHeight: 100.0),  //父
  child: UnconstrainedBox( //“去除”父级限制
    child: ConstrainedBox(
      constraints: BoxConstraints(minWidth: 90.0, minHeight: 20.0),//子
      child: redBox,
    ),
  )
)
```

## 2、线性布局

所谓线性布局，即指沿水平或垂直方向排列子组件。

核心点在于其拥有**主轴**和**交叉轴**，其中**交叉轴就是垂直于主轴的轴**



### Row、Column

* `textDirection`：表示**主轴**方向子组件的布局顺序
* `verticalDirection`：表示**交叉轴**的对齐方向，默认是`VerticalDirection.down`，表示从上到下。
* `mainAxisAlignment`：表示子组件在**主轴**所占用的水平空间内对齐方式
  * `start`：主轴开始方向对齐，受`textDirection`影响
  * `end`:主轴结束方向对齐，受`textDirection`影响
  * `center`：居中对齐
  * `spaceBetween`：剩下的空间平均分布到小部件之间
  * `spaceAround`：剩下的空间平均分布到小部件周围
  * `spaceEvenly`：剩下的空间和小部件一起平均分
* `crossAxisAlignment`：表示子组件在**交叉轴**方向的对齐方式
  * `start`：主轴开始方向对齐，受`textDirection`影响
  * `end`:主轴结束方向对齐，受`textDirection`影响
  * `center`：居中对齐
  * `stretch`:交叉轴上进行拉伸填充
  * `baseline`：基线对齐，受`textBaseline`属性影响
* `mainAxisSize`：表示`Row`在主轴(水平)方向占用的空间

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/093d77e051dc48759fca9a7427e32983~tplv-k3u1fbpfcp-watermark.image?)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/22e8f91b58944038964dca28557729d1~tplv-k3u1fbpfcp-watermark.image?)

## 3、层叠布局

### Stack

- `alignment`：此参数决定如何去对齐没有定位（没有使用`Positioned`）或部分定位的子组件。所谓部分定位，在这里**特指没有在某一个轴上定位：**`left`、`right`为横轴，`top`、`bottom`为纵轴，只要包含某个轴上的一个定位属性就算在该轴上有定位。
- `textDirection`：表示**主轴**方向子组件的布局顺序
- `fit`：此参数用于确定**没有定位**的子组件如何去适应`Stack`的大小。`StackFit.loose`表示使用子组件的大小，`StackFit.expand`表示扩伸到`Stack`的大小。
- `overflow`：此属性决定如何显示超出`Stack`显示空间的子组件；值为`Overflow.clip`时，超出部分会被剪裁（隐藏），值为`Overflow.visible` 时则不会。

### Positioned

通常与`Stack`一起使用，用于在`Stack`中进行定位

```dart
const Positioned({
  Key? key,
  this.left, 
  this.top,
  this.right,
  this.bottom,
  this.width,
  this.height,
  required Widget child,
})
```

## 4、弹性布局

### Flex

`Flex`组件可以沿着水平或垂直方向排列子组件，如果你知道主轴方向，使用`Row`或`Column`会方便一些，**因为`Row`和`Column`都继承自`Flex`**。

```dart
Flex({
  ...
  required this.direction, //弹性布局的方向, Row默认为水平方向，Column默认为垂直方向
  List<Widget> children = const <Widget>[],
})
```

### Expanded

`Expanded`只能作为`Flex`的孩子，因此只能在`Flex`、`Row`、`Column`中使用，按比例“扩伸”`Flex`子组件所占用的空间。

```dart
const Expanded({
  int flex = 1, 
  required Widget child,
})
```

`flex`参数为弹性系数，如果为 0 或`null`，则`child`是没有弹性的，即不会被扩伸占用的空间。如果大于0，所有的`Expanded`按照其 flex 的比例来分割主轴的全部空闲空间。

## 5、流式布局

在介绍 Row 和 Colum 时，如果子 widget 超出屏幕范围，则会报溢出错误，如：

```
Row(
  children: <Widget>[
    Text("xxx"*100)
  ],
);
```

这是因为`Row`默认只有一行，如果超出屏幕不会折行。我们把超出屏幕显示范围会自动折行的布局称为流式布局。Flutter中通过`Wrap`和`Flow`来支持流式布局，将上例中的`Row`换成`Wrap`后溢出部分则会自动折行，下面我们分别介绍`Wrap`和`Flow`。

### Wrap

```dart
Wrap({
  ...
  this.direction = Axis.horizontal,
  this.alignment = WrapAlignment.start,
  this.spacing = 0.0,
  this.runAlignment = WrapAlignment.start,
  this.runSpacing = 0.0,
  this.crossAxisAlignment = WrapCrossAlignment.start,
  this.textDirection,
  this.verticalDirection = VerticalDirection.down,
  List<Widget> children = const <Widget>[],
})
```

- `spacing`：主轴方向子widget的间距
- `runSpacing`：纵轴方向的间距
- `runAlignment`：纵轴方向的对齐方式

### Flow

我们一般很少会使用`Flow`，因为其过于复杂，需要自己实现子 widget 的位置转换，在很多场景下首先要考虑的是`Wrap`是否满足需求。`Flow`主要用于一些需要自定义布局策略或性能要求较高(如动画中)的场景。`Flow`有如下优点：

- 性能好；`Flow`是一个对子组件尺寸以及位置调整非常高效的控件，`Flow`用转换矩阵在对子组件进行位置调整的时候进行了优化：在`Flow`定位过后，如果子组件的尺寸或者位置发生了变化，在`FlowDelegate`中的`paintChildren()`方法中调用`context.paintChild` 进行重绘，而`context.paintChild`在重绘时使用了转换矩阵，并没有实际调整组件位置。
- 灵活；由于我们需要自己实现`FlowDelegate`的`paintChildren()`方法，所以我们需要自己计算每一个组件的位置，因此，可以自定义布局策略。

缺点：

- 使用复杂。
- Flow 不能自适应子组件大小，必须通过指定父容器大小或实现`TestFlowDelegate`的`getSize`返回固定大小。

## 6、对齐与相对定位

### Align

`Align` 组件可以调整子组件的位置，定义如下：

```dart
Align({
  Key key,
  this.alignment = Alignment.center,
  this.widthFactor,
  this.heightFactor,
  Widget child,
})
```

- `alignment` : 需要一个`AlignmentGeometry`类型的值，表示子组件在父组件中的起始位置。`AlignmentGeometry` 是一个抽象类，它有两个常用的子类：`Alignment`和 `FractionalOffset`，我们将在下面的示例中详细介绍。
- `widthFactor`和`heightFactor`是用于确定`Align` 组件本身宽高的属性；它们是两个缩放因子，会分别乘以子元素的宽、高，最终的结果就是`Align` 组件的宽高。如果值为`null`，则组件的宽高将会占用尽可能多的空间。

```dart
 Align(
  widthFactor: 2,
  heightFactor: 2,
  alignment: Alignment(2,0.0),
  child: FlutterLogo(
    size: 60,
  ),
)
```

`Alignment`：`Alignment`继承自`AlignmentGeometry`，表示矩形内的一个点，以**矩形的中心点作为坐标原点**，其有两个属性`x`、`y`，分别表示在水平和垂直方向的偏移

`FractionalOffset`：`FractionalOffset` 继承自 `Alignment `，它和 `Alignment `唯一的区别就是坐标原点不同！`FractionalOffset` 的坐标原点为矩形的左侧顶点

### Center

`Center`继承自`Align`,可以认为`Center`组件其实是对齐方式确定（`Alignment.center`）了的`Align`。

```dart
...//省略无关代码
DecoratedBox(
  decoration: BoxDecoration(color: Colors.red),
  child: Center(
    child: Text("xxx"),
  ),
),
DecoratedBox(
  decoration: BoxDecoration(color: Colors.red),
  child: Center(
    widthFactor: 1,
    heightFactor: 1,
    child: Text("xxx"),
  ),
)
```

## 7、其他

### Card

卡片布局

### ListTile

它可以很轻松的创建一个包含三行文本以及可选的行前和行尾图标的行

### AspectRatio

指定子元素在宽高比方面与父元素相匹配。它通常与其它布局组件（如 `Container`、`Row`、`Column` 等）一起使用，以确保子元素具有所需的宽高比

### LayoutBuilder

通过 LayoutBuilder，我们可以在**布局过程**中拿到父组件传递的约束信息，然后我们可以根据约束信息动态的构建不同的布局。

比如我们实现一个响应式的 Column 组件 ResponsiveColumn，它的功能是当当前可用的宽度小于 200 时，将子组件显示为一列，否则显示为两列。简单来实现一下：

```dart
class ResponsiveColumn extends StatelessWidget {
  const ResponsiveColumn({Key? key, required this.children}) : super(key: key);

  final List<Widget> children;

  @override
  Widget build(BuildContext context) {
    // 通过 LayoutBuilder 拿到父组件传递的约束，然后判断 maxWidth 是否小于200
    return LayoutBuilder(
      builder: (BuildContext context, BoxConstraints constraints) {
        if (constraints.maxWidth < 200) {
          // 最大宽度小于200，显示单列
          return Column(children: children, mainAxisSize: MainAxisSize.min);
        } else {
          // 大于200，显示双列
          var _children = <Widget>[];
          for (var i = 0; i < children.length; i += 2) {
            if (i + 1 < children.length) {
              _children.add(Row(
                children: [children[i], children[i + 1]],
                mainAxisSize: MainAxisSize.min,
              ));
            } else {
              _children.add(children[i]);
            }
          }
          return Column(children: _children, mainAxisSize: MainAxisSize.min);
        }
      },
    );
  }
}


class LayoutBuilderRoute extends StatelessWidget {
  const LayoutBuilderRoute({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    var _children = List.filled(6, Text("A"));
    // Column在本示例中在水平方向的最大宽度为屏幕的宽度
    return Column(
      children: [
        // 限制宽度为190，小于 200
        SizedBox(width: 190, child: ResponsiveColumn(children: _children)),
        ResponsiveColumn(children: _children),
        LayoutLogPrint(child:Text("xx")) // 下面介绍
      ],
    );
  }
}
```

通过`LayoutLogPrint(child:Text("xx"))`可以打印约束信息。

# 四、滚动组件

## 1、ScrollController

`ScrollController`来控制可滚动组件的滚动位置，这不是一个组件，常用的属性和方法：

- `offset`：可滚动组件当前的滚动位置。
- `jumpTo(double offset)`、`animateTo(double offset,...)`：这两个方法用于跳转到指定的位置，它们不同之处在于，后者在跳转时会执行一个动画，而前者不会。

> 滚动监听

```dart
controller.addListener(()=>print(controller.offset))
```

## 2、SingleChildScrollView

`SingleChildScrollView`类似于Android中的`ScrollView`，它只能接收一个子组件，定义如下：

```dart
SingleChildScrollView({
  this.scrollDirection = Axis.vertical, //滚动方向，默认是垂直方向
  this.reverse = false, 
  this.padding, 
  bool primary, 
  this.physics, 
  this.controller,
  this.child,
})
```

> tip：`SingleChildScrollView`只应在期望的内容不会超过屏幕太多时使用，因为其不支持基于 Sliver 的延迟加载模型，可能会导致性能问题

## 3、ListView

`ListView`是最常用的可滚动组件之一，它可以沿一个方向线性排布所有子组件，并且它也支持列表项懒加载（在需要时才会创建）。

```dart
ListView({
  ...  
  //可滚动widget公共参数
  Axis scrollDirection = Axis.vertical,
  bool reverse = false,
  ScrollController? controller,
  bool? primary,
  ScrollPhysics? physics,
  EdgeInsetsGeometry? padding,
  
  //ListView各个构造函数的共同参数  
  double? itemExtent,
  Widget? prototypeItem, //列表项原型，后面解释
  bool shrinkWrap = false,
  bool addAutomaticKeepAlives = true,
  bool addRepaintBoundaries = true,
  double? cacheExtent, // 预渲染区域长度
    
  //子widget列表
  List<Widget> children = const <Widget>[],
})
```

构造方式：

1. 通过`children`参数进行构造

2. 若组件较多，可以通过`ListView.builder`构造

   ```dart
   ListView.builder(
     itemCount: 100,
     itemExtent: 50.0, //强制高度为50.0
     itemBuilder: (BuildContext context, int index) {
       return ListTile(title: Text("$index"));
     }
   );
   ```

3. `ListView.separated`,类似于`ListView.builder`，增加了一个**分割组件**。

   ```dart
   // eg:奇数行添加一条蓝色下划线，偶数行添加一条绿色下划线
   class ListView3 extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
       //下划线widget预定义以供复用。  
       Widget divider1=Divider(color: Colors.blue,);
       Widget divider2=Divider(color: Colors.green);
       return ListView.separated(
         itemCount: 100,
         //列表项构造器
         itemBuilder: (BuildContext context, int index) {
           return ListTile(title: Text("$index"));
         },
         //分割器构造器
         separatorBuilder: (BuildContext context, int index) {
           return index%2==0?divider1:divider2;
         },
       );
     }
   }
   ```

## 4、GridView

`GridView`可以构建一个二维网格列表，与`ListView`基本相同

   ```dart
     GridView({
       Key? key,
       Axis scrollDirection = Axis.vertical,
       bool reverse = false,
       ScrollController? controller,
       bool? primary,
       ScrollPhysics? physics,
       bool shrinkWrap = false,
       EdgeInsetsGeometry? padding,
       required this.gridDelegate,  //下面解释
       bool addAutomaticKeepAlives = true,
       bool addRepaintBoundaries = true,
       double? cacheExtent, 
       List<Widget> children = const <Widget>[],
       ...
     })
   ```

`SliverGridDelegate`是一个抽象类，定义了`GridView Layout`相关接口，子类需要通过实现它们来实现具体的布局算法。Flutter中提供了两个`SliverGridDelegate`的子类`SliverGridDelegateWithFixedCrossAxisCount`和`SliverGridDelegateWithMaxCrossAxisExtent`

* SliverGridDelegateWithFixedCrossAxisCount：横轴为固定数量子元素的layout算法：

  ```dart
  GridView(
    gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3, //横轴三个子widget
        childAspectRatio: 1.0 //宽高比为1时，子widget
    ),
    children:<Widget>[
      Icon(Icons.ac_unit),
      Icon(Icons.airport_shuttle),
      Icon(Icons.all_inclusive),
      Icon(Icons.beach_access),
      Icon(Icons.cake),
      Icon(Icons.free_breakfast)
    ]
  );
  ```

* SliverGridDelegateWithMaxCrossAxisExtent：横轴子元素为固定最大长度的layout算法：

  `maxCrossAxisExtent`为子元素在横轴上的最大长度，之所以是“最大”长度，是**因为横轴方向每个子元素的长度仍然是等分的**，

  ```dart
  GridView(
    padding: EdgeInsets.zero,
    gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
        maxCrossAxisExtent: 120.0,
        childAspectRatio: 2.0 //宽高比为2
    ),
    children: <Widget>[
      Icon(Icons.ac_unit),
      Icon(Icons.airport_shuttle),
      Icon(Icons.all_inclusive),
      Icon(Icons.beach_access),
      Icon(Icons.cake),
      Icon(Icons.free_breakfast),
    ],
  );
  ```

## 5、PageView

如果要实现页面切换和 `Tab `布局，我们可以使用 `PageView `组件。需要注意，`PageView `是一个非常重要的组件，因为在移动端开发中很常用，比如大多数 `App `都包含 `Tab `换页效果、图片轮动以及抖音上下滑页切换视频功能等等，这些都可以通过 `PageView `轻松实现。

```dart
PageView({
  Key? key,
  this.scrollDirection = Axis.horizontal, // 滑动方向
  this.reverse = false,
  PageController? controller,
  this.physics,
  List<Widget> children = const <Widget>[],
  this.onPageChanged,
  
  //每次滑动是否强制切换整个页面，如果为false，则会根据实际的滑动距离显示页面
  this.pageSnapping = true,
  //主要是配合辅助功能用的，后面解释
  this.allowImplicitScrolling = false,
  //后面解释
  this.padEnds = true,
})
```

> **** 页面缓存 ****

`PageView`默认并没有缓存功能，一旦页面滑出屏幕它就会被销毁，这不符合我们主页`Tab`切换的一些场景，详情可以查看缓存部分的介绍[Flutter进阶01-基础](Flutter进阶01-基础.md)

## 6、TabBar

### TabBar

`TabBar`为导航标题，通常与`TabBarView`结合使用。

```dart
const TabBar({
  Key? key,
  required this.tabs, // 具体的 Tabs，需要我们创建
  this.controller,
  this.isScrollable = false, // 是否可以滑动
  this.padding,
  this.indicatorColor,// 指示器颜色，默认是高度为2的一条下划线
  this.automaticIndicatorColorAdjustment = true,
  this.indicatorWeight = 2.0,// 指示器高度
  this.indicatorPadding = EdgeInsets.zero, //指示器padding
  this.indicator, // 指示器
  this.indicatorSize, // 指示器长度，有两个可选值，一个tab的长度，一个是label长度
  this.labelColor, 
  this.labelStyle,
  this.labelPadding,
  this.unselectedLabelColor,
  this.unselectedLabelStyle,
  this.mouseCursor,
  this.onTap,
  ...
}) 
```

### TabBarView

`TabBarView`封装了`PageView`

```
 TabBarView({
  Key? key,
  required this.children, // tab 页
  this.controller, // TabController
  this.physics,
  this.dragStartBehavior = DragStartBehavior.start,
}) 
```

### DefaultTabController

通过`TabController`进行联动和控制

```dart
class TabViewRoute1 extends StatefulWidget {
  @override
  _TabViewRoute1State createState() => _TabViewRoute1State();
}

class _TabViewRoute1State extends State<TabViewRoute1>
    with SingleTickerProviderStateMixin {
  late TabController _tabController;
  List tabs = ["新闻", "历史", "图片"];

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: tabs.length, vsync: this);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("App Name"),
        bottom: TabBar(
          controller: _tabController,
          tabs: tabs.map((e) => Tab(text: e)).toList(),
        ),
      ),
      body: TabBarView( //构建
        controller: _tabController,
        children: tabs.map((e) {
          return KeepAliveWrapper(
            child: Container(
              alignment: Alignment.center,
              child: Text(e, textScaleFactor: 5),
            ),
          );
        }).toList(),
      ),
    );
  }
  
  @override
  void dispose() {
    // 释放资源
    _tabController.dispose();
    super.dispose();
  }
}
```

> 通常会创建一个 `DefaultTabController `作为它们共同的父级组件，这样它们在执行时就会从组件树向上查找，都会使用我们指定的这个 `DefaultTabController`。

```dart
class TabViewRoute2 extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    List tabs = ["新闻", "历史", "图片"];
    return DefaultTabController(
      length: tabs.length,
      child: Scaffold(
        appBar: AppBar(
          title: Text("App Name"),
          bottom: TabBar(
            tabs: tabs.map((e) => Tab(text: e)).toList(),
          ),
        ),
        body: TabBarView( //构建
          children: tabs.map((e) {
            return KeepAliveWrapper(
              child: Container(
                alignment: Alignment.center,
                child: Text(e, textScaleFactor: 5),
              ),
            );
          }).toList(),
        ),
      ),
    );
  }
}
```

## 7、CustomScrollView

`CustomScrollView`组件来帮助我们创建一个公共的`Scrollable`和`Viewport`，然后它的`slivers`参数接受一个`saliver`数组，这样我们就可以使用`CustomScrollView`处理滑动冲突。

```dart
Widget buildTwoSliverList() {
  // SliverFixedExtentList 是一个 Sliver，它可以生成高度相同的列表项。
  // 再次提醒，如果列表项高度相同，我们应该优先使用SliverFixedExtentList 
  // 和 SliverPrototypeExtentList，如果不同，使用 SliverList.
  var listView = SliverFixedExtentList(
    itemExtent: 56, //列表项高度固定
    delegate: SliverChildBuilderDelegate(
      (_, index) => ListTile(title: Text('$index')),
      childCount: 10,
    ),
  );
  // 使用
  return CustomScrollView(
    slivers: [
      listView,
      listView,
    ],
  );
}
```

### SliverToBoxAdapter

在实际布局中，我们通常需要往`CustomScrollView`中添加一些自定义的组件，而这些组件并非都有`Sliver`版本，为此`Flutter`提供了一个`SliverToBoxAdapter`组件，它是一个适配器：可以将`RenderBox`适配为`Sliver`。

```dart
CustomScrollView(
  slivers: [
    SliverToBoxAdapter(
      child: SizedBox(
        height: 300,
        child: PageView(
          children: [Text("1"), Text("2")],
        ),
      ),
    ),
    buildSliverFixedList(),
  ],
);
```

### SliverPersistentHeader

`SliverPersistentHeader`的功能是当滑动到`CustomScrollView`的顶部时，可以将组件固定在顶部。

```dart
const SliverPersistentHeader({
  Key? key,
  // 构造 header 组件的委托
  required SliverPersistentHeaderDelegate delegate,
  this.pinned = false, // header 滑动到可视区域顶部时是否固定在顶部
  this.floating = false, // 正文部分介绍
})
```

`floating`的做用是：`pinned`为`false`时 ，则header可以滑出可视区域（`CustomScrollView`的`Viewport`）（不会固定到顶部），当用户再次向下滑动时，此时不`header`已经被滑出了多远，它都会立即出现在可视区域顶部并固定住，直到继续下滑到`header`在列表中原来的位置时，`header`才会重新回到原来的位置（不再固定在顶部）。 具体效果，我们后面会有示例，读者可以实际运行起来看看效果。

## 8、NestedScrollView

我们知道 CustomScrollView 只能组合 Sliver，如果有孩子也是一个可滚动组件（通过 SliverToBoxAdapter 嵌入）且它们的滑动方向一致时便不能正常工作。为了解决这个问题，Flutter 中提供了一个NestedScrollView 组件，它的功能时组合（协调）两个可滚动组件

```dart
const NestedScrollView({
  ... //省略可滚动组件的通用属性
  //header，sliver构造器
  required this.headerSliverBuilder,
  //可以接受任意的可滚动组件
  required this.body,
  this.floatHeaderSlivers = false,
}) 
```

```dart
Material(
  child: NestedScrollView(
    headerSliverBuilder: (BuildContext context, bool innerBoxIsScrolled) {
      // 返回一个 Sliver 数组给外部可滚动组件。
      return <Widget>[
        SliverAppBar(
          title: const Text('嵌套ListView'),
          pinned: true, // 固定在顶部
          forceElevated: innerBoxIsScrolled,
        ),
        buildSliverList(5), //构建一个 sliverList
      ];
    },
    body: ListView.builder(
      padding: const EdgeInsets.all(8),
      physics: const ClampingScrollPhysics(), //重要
      itemCount: 30,
      itemBuilder: (BuildContext context, int index) {
        return SizedBox(
          height: 50,
          child: Center(child: Text('Item $index')),
        );
      },
    ),
  ),
);
```
