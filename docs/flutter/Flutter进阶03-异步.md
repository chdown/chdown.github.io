# 异步编程

## await

```
Future<String> lookUpVersion() async {
    retutn Future.value("1.2.0")
}

Futrue<void> checkVersion() async{
    // 第一种：必须在带有 async 关键字的 异步函数 中使用 await
    var version = await lookUpVersion();
}
```

## then

```dart
void main() async {
    var userId = await _fetchUserId();
    var friendId = await _fetchFriendId(userId);
    var videoList = await _fetchVideoList(friendId);
}
void main() {
    _fetchUserId().then((userId) {
        return _fetchFriendId(userId)；
    }).then((friendId) {
        return _fetchVideoList(friendId)；
    }).then((videoList) {
        // println
    })
}
```

## 错误和状态

```dart
fun getData async {
    print("start")
    Future(() {
        throw Exception("异常");
    }).then((value) => print(value)])
      .whenComplate((value) => print("完成回调")])
      .catchError((e) => print("异常：${e.toSting()}")]);
}
```

> tip：如果使用`catchError`必须放在链式调用的最后

## 队列

**异步代码是有序**的，不需要添加`awiat`,需要`async`修饰 。

```dart
void testFuture() async {
  Future(() {
    return '任务1';
  }).then((value) => print('$value结束'));

  Future(() {
    sleep(Duration(seconds: 1));
    return '任务2';
  }).then((value) => print('$value结束'));

  Future(() {
    return '任务3';
  }).then((value) => print('$value结束'));

  Future(() {
    return '任务4';
  }).then((value) => print('$value结束'));

  print('任务添加完毕');
}

// 输出
任务1结束
任务2结束
任务3结束
任务4结束
```

**链式依赖关系**

```dart
void testFuture1() {
  Future(() {
    sleep(Duration(seconds: 1));
    return '任务1';
  }).then((value) {
    print('$value结束');
    return '$value任务2';
  }).then((value) {
    print('$value结束');
    return '$value任务3';
  }).then((value) {
    print('$value结束');
    return '$value任务4';
  });

  print('任务添加完毕');
}

// 输出
任务1结束
任务1任务2结束
任务1任务2任务3结束
任务1任务2任务3任务4结束
```

**异步任务的并行**

```dart
void testFuture2() {
  Future.wait([
    Future(() {
      print('任务A 执行');
      return '任务A';
    }),
    Future(() {
      sleep(Duration(seconds: 1));
      print('任务B 执行');
      return '任务B';
    }),
    Future(() {
      print('任务C 执行');
      return '任务C';
    }),
  ]).then((value) => print(value[1] + value[0] + value[2]));
}

// 输出
任务A 执行
任务B 执行
任务C 执行
任务A任务B任务C
```

## 微任务

微任务的优先级第，但是序列等级高，因此会优先执行

```dart
void testFuture3() {
  print('外部代码1');

  Future(() => print('A')).then((value) => print('A结束'));
  Future(() => print('B')).then((value) => print('B结束'));

  scheduleMicrotask(() {
    print('微任务A');
  });
  sleep(Duration(seconds: 2));
  print('外部代码2');
}

// 输出
外部代码1
外部代码2
微任务A
A
A结束
B
B结束
```

## 示例

```dart
void testFuture4() {
  Future x1 = Future(() => null);
  Future x2 = x1.then((value) {
    print('6');
    scheduleMicrotask(() => print('7'));
  });

  x2.then((value) => print('8'));

  Future x = Future(() => print('1'));
  x.then((value) {
    print('4');
    Future(() => print('9'));
  }).then((value) => print('10'));

  Future(() => print('2'));

  scheduleMicrotask(() => print('3'));

  print('5');
}

// 输出
5
3
6
8
 7
1
4
10
2
9
```

