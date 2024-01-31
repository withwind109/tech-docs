在 Flutter 中，Widget 的生命周期可以从它们被创建到销毁的过程中理解。不过，由于`StatelessWidget`和`StatefulWidget`的特性不同，它们的生命周期也有所区别。

### StatelessWidget 生命周期

`StatelessWidget` 的生命周期相对简单，因为它不涉及状态的变化。其生命周期主要包括以下几个步骤：

1. **创建**：当`StatelessWidget`被引入到布局中时，Flutter 框架会调用其构造函数创建一个实例。
2. **构建**：Flutter 框架调用`build`方法来构建 Widget，这个方法会根据给定的`BuildContext`返回一个 Widget 树。
3. **更新**：当父 Widget 需要重建或者配置发生变化时，Flutter 框架会创建一个新的 Widget 实例，并再次调用`build`方法。
4. **销毁**：当`StatelessWidget`从布局中移除时，Flutter 框架会销毁这个 Widget 实例。

### StatefulWidget 生命周期

`StatefulWidget` 的生命周期更为复杂，因为它涉及到状态的管理。其生命周期主要包括以下几个步骤：

1. **创建**：当`StatefulWidget`被引入到布局中时，Flutter 框架会调用其构造函数创建一个实例，并为其创建一个对应的`State`对象。
2. **初始化**：Flutter 框架调用`State`对象的`initState`方法，这是你可以进行状态初始化的地方。这个方法只会被调用一次。
3. **构建**：Flutter 框架调用`State`对象的`build`方法来构建 Widget，这个方法可能会被多次调用，每次状态改变时都会被调用。
4. **更新**：
    - 当父 Widget 需要重建或者配置发生变化时，Flutter 框架会调用`didUpdateWidget`方法，你可以在这里对旧的 Widget 和新的 Widget 进行比较，并进行相应的状态更新。
    - 如果使用`setState`方法更新状态，`build`方法会被再次调用来反映新的状态。
5. **销毁**：当`StatefulWidget`从布局中移除时，Flutter 框架会调用`dispose`方法，你可以在这里进行最终的清理工作。

### 代码举例

下面是一个`StatelessWidget`的简单示例：

```dart
class StatelessExample extends StatelessWidget {
  final String title;

  const StatelessExample({Key key, this.title}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Text(title);
  }
}
```

对于`StatefulWidget`，我们可以使用之前提到的计数器示例，但这次我们将添加`initState`和`dispose`方法来展示其生命周期：

```dart
class StatefulExample extends StatefulWidget {
  @override
  _StatefulExampleState createState() => _StatefulExampleState();
}

class _StatefulExampleState extends State<StatefulExample> {
  int _counter = 0;

  @override
  void initState() {
    super.initState();
    // 初始化状态
    _counter = 0;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Stateful Widget Lifecycle'),
      ),
      body: Center(
        child: Text('Counter: $_counter'),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          setState(() {
            _counter++;
          });
        },
        child: Icon(Icons.add),
      ),
    );
  }

  @override
  void dispose() {
    // 清理工作
    super.dispose();
  }
}
```

在这个`StatefulExample`中，我们可以看到`initState`在创建时被调用来初始化计数器，而`dispose`方法会在 Widget 被销毁时调用。每次点击浮动按钮时，`setState`会被调用，导致`build`方法重新运行以更新显示的计数器值。
