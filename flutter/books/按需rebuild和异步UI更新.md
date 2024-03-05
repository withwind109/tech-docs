## 按需 rebuild

在 Flutter 中，"按需 rebuild"是指当组件的状态发生变化时，只有依赖于这些状态的组件会重新构建。这是通过使用`setState`方法来实现的，它标记当前的`State`对象为脏（dirty），从而触发框架在下一个动画帧中重建这个`State`对象的 UI。

以下是一个简单的 Flutter 代码示例，演示了如何使用`setState`来实现按需 rebuild：

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      // 这个函数是用来更新状态的，并告诉Flutter框架这个State对象的状态已经改变
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    // 这个方法会在setState被调用后重新运行，从而实现UI的更新
    return Scaffold(
      appBar: AppBar(
        title: Text('Flutter Demo'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.headline4,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}
```

在这个例子中，我们有一个`MyHomePage`组件，它是一个`StatefulWidget`。它的状态是一个名为`_counter`的整数。每当用户点击浮动操作按钮时，`_incrementCounter`方法会被调用，这个方法通过`setState`更新`_counter`的值，并触发 UI 的重建。在`build`方法中，我们使用`_counter`的值来更新显示在屏幕上的文本。因为`setState`被调用，所以只有包含`_counter`的`Text`组件和它的父组件会被重新构建，而不是整个应用程序的 UI。这就是按需 rebuild 的工作原理。

## 异步 UI 更新

Flutter 中的异步 UI 更新机制允许开发者执行耗时的操作（如网络请求、数据库访问等）而不会阻塞 UI 线程。这是通过 Dart 的异步编程特性实现的，主要涉及`Future`对象、`async`和`await`关键字。

当你执行一个异步操作时，你可以使用`async`关键字来标记一个函数，这表明函数内部可能会有一些耗时的操作。在函数体内，你可以使用`await`关键字来等待一个异步操作的结果。这样，Dart 的事件循环可以处理其他任务，直到这个异步操作完成。一旦完成，你可以使用`setState`来更新 UI。

以下是一个简单的 Flutter 代码示例，演示了如何使用异步操作来更新 UI：

```dart
import 'package:flutter/material.dart';
import 'dart:async'; // 引入异步编程的库

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Async UI Demo',
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  String _data = 'No data';

  // 模拟一个异步操作，比如网络请求
  Future<String> fetchData() async {
    // 模拟网络延迟
    await Future.delayed(Duration(seconds: 4));
    return 'Fetched Data';
  }

  void _updateData() async {
    String data = await fetchData(); // 等待异步操作的结果
    setState(() {
      // 更新状态并触发UI重建
      _data = data;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Flutter Async UI Demo'),
      ),
      body: Center(
        child: Text(_data),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _updateData, // 当按钮被按下时，触发异步操作
        tooltip: 'Fetch Data',
        child: Icon(Icons.cloud_download),
      ),
    );
  }
}
```

在这个例子中，我们有一个`MyHomePage`组件，它是一个`StatefulWidget`。它的状态包含一个名为`_data`的字符串。当用户点击浮动操作按钮时，`_updateData`方法会被调用，这个方法是异步的，它等待`fetchData`异步操作的结果。`fetchData`函数模拟了一个耗时的网络请求，它使用`Future.delayed`来模拟 4 秒的延迟。一旦数据被“获取”，`_updateData`方法使用`setState`来更新`_data`的值，并触发 UI 的重建。这样，UI 就可以显示新获取的数据，而在等待过程中，应用程序的 UI 仍然是响应的，用户可以进行其他操作。这就是 Flutter 中异步 UI 更新的工作原理。
