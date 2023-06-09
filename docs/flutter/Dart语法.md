## 区别

|   名称   |                 Kotlin                 |                             dark                             |
| :------: | :------------------------------------: | :----------------------------------------------------------: |
| 空运算符 |           `val v= str ?: ""`           |             `val v= str ??= ""`/`print(1 ?? 3)`              |
|  空处理  |                  `?:`                  |                             `??`                             |
|   map    |     `mapof("a" to "b","c" to "d")`     |                     `{"a":"b","c":"d"}`                      |
|   级联   |     `apply`、`run`、`also`、`with`     | ![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/46b9275391d844169fb770e1097bafc2~tplv-k3u1fbpfcp-watermark.image?) |
| 可选参数 | `int sum(int a, int b = 2, int c = 3)` |           `int sum(int a, [int b = 2, int c = 3])`           |
| 命名参数 |                   --                   | ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1faecd1578ee42edbced4c6980950df5~tplv-k3u1fbpfcp-watermark.image?) |
| 参数私有 |              `private a`               |                             `_a`                             |
|   整除   |                   /                    |                              ~/                              |
|          |                                        |                                                              |
|          |                                        |                                                              |

## 基本类型

```dart
// var：类型推导，如果没有初始值，可以变成任何类型
var name = 'Bob';
// Object：动态任意类型，编译阶段检查类型
Object name = 'Bob';
// 指定类型
String name = 'Bob';
// dynamic：动态任意类型，可以绕过编译检查
dynamic name = 'Bob';

// ****延迟初始化，使用前必须初始化，否则会报异常****	
late String description;
```

>`final`：通常用于参数定义
>
>`const`：通常用于定义全局参数

## 数组和集合

```dart
//  扩展操作符（...）和 空感知扩展操作符（...?），插入多个元素
var list = [1, 2, 3];
var list2 = [0, ...list];
var list2 = [0, ...?list];
var list3 = List.generate(10, (index) {
    return index;
});

// 集合中支持 if for
var nav = ['Home', 'Furniture', 'Plants', if (promoActive) 'Outlet'];
var listOfInts = [1, 2, 3];
var listOfStrings = ['#0', for (var i in listOfInts) '#$i'];

// 集合创建
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
var names = <String>{};
// map
var nobleGases = {
  2: 'helium',
  10: 'neon',
};
var gifts = Map<String, String>();
```

## 函数

```dart
// 基本函数
int add(int x) {
  return x + 1;
}

// 可以省略返回值
add(int x) {
  return x + 1;
}

// 只包含一个表达式时
add(int x) => return x + 1;

// 可选参数
int add(int x, [int? y, int? z])

// 可选参数 默认值
int add(int x, [int y = 1, int z = 2])
    
// 命名参数 默认值
int add({int x = 1, int y = 1, int z = 1})

// 将函数作为参数传递给另一个函数
void printElement(int element) {
  print(element);
}
var list = [1, 2, 3];
list.forEach(printElement);

// Funcation 返回函数对象
Function makeAdd(int x) {
  return (int y) => x + y;
}
var add = makeAdd(1);
print(add(5));
```

## 类

> 类中的私有方法或对象，需要添加”_”

```dart
class Point {
  int x, y;
  Map origin1, origin2;
    
  // get、set简化写法
  String get name => 'people is $_name';
  set name(String value) => _name = value;
    
  // 构造函数，可以添加const
  Point(this.x, this.y);
    
  // 初始化列表
  Point(this.x, this.y)
    : origin1 = {'x': x, 'y': y},
      origin2 = {'x': x + 10, 'y': y + 10};
    
  // 命名构造函数
  Point.fromJson(Map json)
    : x = json['x'],
      y = json['y'],
      origin1 = {'x': json['x'], 'y': json['y']},
      origin2 = {'x': json['x'] + 10, 'y': json['y'] + 10}.
   
  // 重定向构造函数
  Point.fromJson(Map json) : this(json['x'], json['y']);

  // callable
  call(String num) {}
  // callable调用：Point(1, 2)(3)
}
```

## 扩展方法

```dart
// 定义
extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }
}
```

## with

```dart
class Phone {
  void call() {
    print('Phone is calling...');
  }
}

class Android {
  void playStore() {
    print('Google play store');
  }
}

class Xiaomi with Phone, Android {
    
}
```

## mixin

一个类可以混入若干个类，此类可以同时拥有这混入类的能力

```dart
mixin MoveAble{
  double speed = 10;
  void move(){
    print("=====$runtimeType move====");
  }
}

class Shape with MoveAble{

}
```

## stram

`Stream` 也是用于接收异步事件数据，和 `Future` 不同的是，它可以接收多个异步操作的结果（成功或失败）。 也就是说，在执行异步任务时，可以通过多次触发成功或失败事件来传递结果数据或错误异常。 `Stream` 常用于会多次读取数据的异步任务场景，如网络内容下载、文件读写等。举个例子：

```dart
Stream.fromFutures([
  // 1秒后返回结果
  Future.delayed(Duration(seconds: 1), () {
    return "hello 1";
  }),
  // 抛出一个异常
  Future.delayed(Duration(seconds: 2),(){
    throw AssertionError("Error");
  }),
  // 3秒后返回结果
  Future.delayed(Duration(seconds: 3), () {
    return "hello 3";
  })
]).listen((data){
   print(data);
}, onError: (e){
   print(e.message);
},onDone: (){

});
```

上面的代码依次会输出：

```text
I/flutter (17666): hello 1
I/flutter (17666): Error
I/flutter (17666): hello 3
```

