# GetX

GetX是一个高性能的，强大的且易用Flutter应用开发框架，用于管理状态，操作简单、便捷，结构清晰。纵观其设计理念，可以将其概括为三个核心概念——路由管理，依赖管理和状态管理。

在yaml文件中添加该库的依赖：
```
dependencies:
  get: any
```

也可以通过本地目录引入：
```
 get:
    path: ./third_plugins/get
```

## 使用方法举例：

### 1、状态管理：

GetX 提供了两种响应式状态管理的方法：响应式变量方式和状态管理器方式。

#### 响应式变量

只需在变量的末尾加上一个`.obs`就可将变量定义为响应式变量：

```dart
final name = ''.obs;
final isLogged = false.obs;
final count = 0.obs;
final balance = 0.0.obs;
final items = <String> [].obs;
final testMap = <String, int> {}.obs;
```

使用的时候调用`xx.value`即可拿到变量的值，不过对于`List 、Map`则不需要加`.value`。

```dart
String nameValue = name.value;
bool boolValue = isLogged.value;
int countValue = count.value;
double numberValue = number.value;
String item = items[0]; 
int value = testMap['key'];
```

对于基础数据类型，只需要对`.value`重新赋值并且通过`Obx`绑定目标控件即可更新数据并刷新界面：

```dart
name.value = "123";
isLogged.value = true;
count.value = 1;
number.value = 12.0;
```

对于其他数据类型需要调用`update`或者变量方法更新，如下：

```dart
user.update((value) { value?.name = "123";});
```

或者使用变量名方法重新赋值一个对象，比如变量名为`user`则可使用`user()`方法进行更新:

```dart
user(User(name: "test", age: 25));
```

在界面上使用响应式变量只需在控件上包裹`Obx`实现绑定即可实现响应式更新：

```dart
Obx(() => Text("${count.value}"))
```

#### 数据变化监听

除了使用`Obx`实现界面数据自动刷新外，GetX提供了多种手动方式对响应式变量进行数据变化监听，当数据发生变化时执行自定义的逻辑，比如数据变更后重新请求接口等。

* `ever` 当数据发生改变时触发
* `everAll` 和 "ever "很像，只是监听的是多个响应式变量的变化，当其中一个发生变化就会触发回调
* `once` 只在变量第一次被改变时被调用
* `debounce` 防抖，即延迟一定时间调用，且在规定时间内只有最后一次改变会触发回调。如设置时间为 1 秒，发生了3次数据变化，每次间隔500毫秒，则只有最后一次变化会触发回调。
* `interval` 时间间隔内只有最后一次变化会触发回调。如设置时间间隔为1秒，则在1秒内无论点击多少次都只有最后一次会触发回调，然后进入下一次的时间间隔。

这里创建一个名为MyController的控制器，内部有count字段，我们想要在点击时将其增加1。
```dart
class TestController extends GetxController {
  var count = 0.obs; // obs将变量转化为可观察对象
}
```

```dart
// 每次`count`变化时调用。
ever(count, (newValue) => print("$newValue has been changed"));
// 只有在变量count在第一次被改变时才会被调用。
once(count, (newValue) => print("$newValue was changed once"));
// 防DDos - 每当用户停止输入1秒时调用，例如。
debounce(count, (newValue) => print("debouce$newValue"), time: Duration(seconds: 1));
// 忽略1秒内的所有变化，只有最后一次会触发回调。
interval(count, (newValue) => print("interval $newValue"), time: Duration(seconds: 1));
```

在页面中使用自定义的控制器

```dart
class GetXControllerExample extends StatelessWidget {
  final TestController controller = Get.put(TestController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('demo'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            Obx(() => Text("${controller.count}")),
            ElevatedButton(
                onPressed: () {
                  controller.count++;
                  print(controller.count);
                },
                child: Text("click add 1"))
          ],
        ),
      ),
    );
  }
}
```

一个使用列表的示例，使用的是Obx来监听变更：

```dart
class Item {
  final String title;

  Item({required this.title});
}

class TestController extends GetxController {
  var listItems = List<Item>.empty(growable: true).obs;

  void addItem(Item item) {
    listItems.add(item);
  }

  void removeItem(Item item) {
    listItems.remove(item);
  }
}

class GetXControllerExample extends StatelessWidget {
  final TestController testController = Get.put(TestController());
  final textFieldController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: const Text('demo'),
        ),
        body: Column(
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: Row(
                children: <Widget>[
                  Container(
                    width: 220,
                    child: TextField(
                      controller: textFieldController,
                      decoration: InputDecoration(
                        labelText: 'Enter text here',
                      ),
                    ),
                  ),
                  ElevatedButton(
                    onPressed: () {
                      testController
                          .addItem(Item(title: textFieldController.text));
                    },
                    child: Text(
                      'Button',
                      style: TextStyle(
                        color: Colors.white,
                      ),
                    ),
                  ),
                ],
              ),
            ),
            Expanded(
                child: Obx(() => ListView.builder(
                    itemCount: testController.listItems.length,
                    itemBuilder: (context, index) {
                      return ListTile(
                        title: Text(testController.listItems[index].title),
                        trailing: ElevatedButton(
                          child: const Text("delete"),
                          onPressed: () {
                            testController
                                .removeItem(testController.listItems[index]);
                          },
                        ),
                      );
                    }))),
          ],
        ));
  }
}
```

也可以使用`GetBuilder`和`update`的方式组合来更新状态：

```dart
...
void addItem(Item item) {
  listItems.add(item);
  update();
}

void removeItem(Item item) {
  listItems.remove(item);
  update();
}
...

...
Expanded(
  child: GetBuilder<TestController>(
  init: testController,
  builder: (controller) {
    return ListView.builder(
        itemCount: controller.listItems.length,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text(controller.listItems[index].title),
            trailing: ElevatedButton(
              child: const Text("delete"),
              onPressed: () {
                controller.removeItem(controller.listItems[index]);
              },
            ),
          );
        });
  },
))
...
```

GetBuilder` 组件提供了 `tag` 和 `id` 这两个参数，这两个参数的作用主要在于控制器实例的访问和更新机制的管理。

1. `tag` 参数的作用：

`tag` 参数用于在 GetX 的依赖注入系统中标识控制器的实例。默认情况下，当你使用 `Get.put()` 或者 `Get.lazyPut()` 方法将控制器放入 GetX 依赖注入系统中时，它们会被存储在一个默认的空标签下。然而，如果你想在同一个应用中使用多个相同类型的控制器实例，你可以为它们指定不同的 `tag`。

举例来说，如果你有两个页面都需要 `UserController` 的实例，但是对于不同的用户，你可以这样操作：

```dart
Get.put(UserController(user: User1), tag: 'user1');
Get.put(UserController(user: User2), tag: 'user2');
```

在 UI 中使用 `GetBuilder` 获取特定的控制器实例：

```dart
GetBuilder<UserController>(
  tag: 'user1',
  builder: (controller) {
    // 这里的 controller 是 tag 为 'user1' 的 UserController 实例
  },
);
```


2. `id` 参数的作用：

`id` 参数用于 `GetBuilder`，它与 `update()` 方法结合起来，让你可以更精细地控制哪些 `GetBuilder` 需要重新构建。不是每次状态改变都需要更新所有的 `GetBuilder`，有时候你只想更新特定的那个 `GetBuilder`。

在控制器内部，当你调用 `update()` 方法时，你可以将 `id` 作为参数传递给它：

这里是一个如何使用 `id` 的例子：

```dart
class CounterController extends GetxController {
  int counter = 0;

  void increment() {
    counter++;
    update([‘counter’]); // 只更新具有 'counter' id 的 GetBuilder
  }
}

class MyHomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        GetBuilder<CounterController>(
          id: 'counter', // 给这个 GetBuilder 指定一个 id
          builder: (_) {
            return Text('You have pressed the button ${_.counter} times');
          },
        ),
        GetBuilder<CounterController>(
          // 由于我们没有给这个 GetBuilder 指定 id 'counter'
          // 它不会在调用 update(['counter']) 时更新
          builder: (_) {
            return Text('This will not update on increment');
          },
        ),
      ],
    );
  }
}
```

在此例中，`increment` 函数通过传递 `id` 列表 `['counter']` 调用 `update` 方法，确保只有设置了 `'counter'` `id` 的 GetBuilder 会被重建。这样，当你有一个大型页面，其中只有少量小部分需要更新时，这可以避免不必要的 UI 重建，提升应用性能。

### 2、路由管理：

在 Flutter 中进行页面跳转就是通过路由实现，GetX 提供了`普通路由`和`别名路由`。

#### 普通路由

```dart
Get.to(CounterPage()); // to进入下一个页面
Get.to(CounterPage(), arguments: count); // arguments可以传参，下个页面通过dynamic args = Get.arguments;获取参数
Get.off(CounterPage()); // 进入下一个界面，且导航没有返回
Get.offAll(CounterPage()); // 进入下一个界面并取消之前的所有路由
Get.back(); // 返回
Get.back(result: 'success'); // 返回传参 ，获取返回参数var data = await Get.to(CounterPage());
```

#### 别名路由

GetX 对Flutter的路由进行了封装，提供了更易用的API。


```dart

// 首先创建一个`RouteMaps`（名字自己定义）的类，用于统一配置路由映射关系：
class RouteGet {  
  // name  
  static const String first = "/first";
  static const String second = "/second";
  // maps  定义别名与页面的映射关系。
  static final List<GetPage> getPages = [    
      GetPage(name: '/first', page: () => FirstRoute()),
      GetPage(name: '/second', page: () => SecondRoute()),
  ];
}

// 然后在`GetMaterialApp`进行`initialRoute`和`getPages`的配置，即初始页面和路由映射集合:
class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
        title: 'Flutter Demo',
        initialRoute: RouteGet.first,
        getPages: RouteGet.getPages
  }
}

// 路由跳转:
Get.toNamed(RouteGet.second);
// 路由传参:
Get.toNamed(RouteGet.login, arguments: {"name":"a"});
// 直接在路由别名后面跟参数
Get.toNamed("/second?device=android&id=3&name=Enzo");
// 接收参数:
// 通过 arguments 进行传参，在下个页面接收参数直接使用 Get.arguments 获取到传递过来的参数
dynamic args = Get.arguments;
// 使用别名后 Url 传递参数的方式，使用 Get.parameters 获取参数：
Get.parameters['device'];
```

#### Bindings:

Bindings 主要是配合路由进行使用，当通过 GetX 路由进入页面时，会自动调用 dependencies 方法， 可以在这里进行依赖关系的注册\初始化等。

```dart
class CounterBinding implements Bindings {
  @override
  void dependencies() {
    Get.lazyPut<CounterController>(() => CounterController());
    Get.put<Service>(() => Api());
  }
}

// 普通路由使用：
Get.to(CounterPage(), binding: CounterBinding());

// 别名路由使用，在GetPage中设置路由对应的Bindings
// map 
static final List<GetPage> getPages = [
  GetPage(name: counter, page: () => CounterPage(), binding: CounterBinding())
];
```

### 3、依赖管理：

GetX对依赖管理提供了很好的解决方案，可以非常方便地获取数据或者对象。 依赖管理主要是通过Get.put(), Get.find()和Get.lazyPut()等方法来实现的。第一次使用某个 Controller 时需要使用`Get.put()`进行初始化，后续再使用同一个 Controller 就不需要再进行初始化，直接通过`Get.find()`来获取实例就可以了。


#### 1、Get.put():

这是一个类注入的实例，你可以在任何地方注入你需要的类，创建一个新的实例。
```dart
void main() {
  Get.put<CounterController>(CounterController());
  Get.put<CounterController>(CounterController(), permanent: true); // permanent:是否永久，默认false当实例不再使用时会进行销毁，true则会一直保留 
  Get.put<CounterController>(CounterController, tag: "counter"); // 标签，用于区分同一个类不同实例

  runApp(App());
}

```

#### 2、Get.find():

我们可以通过 `Get.find()` 找到已经注入的类的实例。
```dart
class SomeClass extends StatelessWidget {
  Controller c = Get.find();
  @override
  Widget build(context) {
    return Text("${c.count}");
  }
}
```

#### 3、Get.lazyPut():

lazyPut() 方法和 put() 方法含义相同，但和 put() 方法不同点在于，当使用 lazyPut() 时，只有在你真正用到的时候才会初始化这个类的实例。

```dart
void main() {
  Get.lazyPut(() => Controller());
  Get.lazyPut<CounterController>(  () { return CounterController();}, tag: Math.random().toString(), fenix:true );
  // fenix：类似'永久'，不同的是，当不使用时，实例会被丢弃，但当再次需要使用时，Get会重新创建实例
  // tag：标签，用于区分同一个类不同实例。
  runApp(App());
}
```
这样就可以将需要使用的实例延迟初始化，当真正使用的时候再加载，以提高性能。 

#### 4、Get.putAsync():
可以异步注册一个实例。用于某些实例需要异步初始化时使用，比如`SharedPreferences`:

```dart
Get.putAsync<SharedPreferences>(() async {  
  final prefs = await SharedPreferences.getInstance();  
  await prefs.setInt('counter', 12345); 
  return prefs;
});

```
同样拥有`permanent`和`tag`参数，作用一样。

#### 5、Get.create():

`create`与`put`使用方式上基本类似，不同的是它的`permanent`默认为`true`。

```dart
Get.create<CounterController>(() => CounterController());
```

6、Get.delete():
移除注入的依赖实例，大部分情况下不需要手动调用该方法，GetX 内部会自动处理，当不需要时自动移除。

```dart
Get.delete<CounterController>();
```


### 4、GetXController

Getx提供了GetXController这样一个生命周期GetX控制器的生命周期函数定义在Getx控制器，它有五个生命周期回调——onInit, onReady, onClose, onDelete 和 onDetached。

#### 下面是各个生命周期函数的使用方法：

1. onInit：这是Getx控制器的第一个生命周期方法，它将在控制器实例化的时候立即被调用。使用该方法初始化一些变量或者启动某些函数。

```dart
class MyController extends GetxController{
  @override
  void onInit(){
    print("Init method Called");
    super.onInit();
  }
}
```

2. onReady：onReady是第二个生命周期方法，它在onInit方法调用结束并且widget被插入渲染树中后调用。这里，你可以启动一些大量的数据或多媒体Controller类中，这些函数包括：

1. `onInit()`: 这个方法在`GetxController`的子类被实例化并插入到`GetBuilder`时立即被调用。`onInit`在插入边界之前被调用，因此在onInit内部可以访问上下文的数据。`onInit`是异步的，因此可以使用异步函数。

2. `onReady()`: 一旦`GetxController`的子类被实例化并插入到`GetBuilder`中并更新了当前实例后，这个方法就会被调用。 同样，`onReady`也是异步的。

3. `onClose()`: 当`GetxController`的生命周期结束(即，当它被删除内存。
```dart
class MyController extends GetxController{
  @override
  void onReady() {
    print("Ready method Called");
    super.onReady();
  }
}
```
3. onClose：onClose是控制器的最后一个生命周期方法，它将在控制器被消除的时候调用。你可以清理一些不再使用的控制器和变量。
```dart
class MyController extends GetxController{
  @override
  void onClose() {
    print("Close method Called");
    super.onClose();
  }
}
```
4. onDelete 和onDetached：

这两个生命周期函数只在GetBuilder中有。onDelete当控制器被永久删除时调用，清除控制器和变量。onDetached当一个控制器不再被找到时时)会调用这个事件。它是最后一个被调用的方法，用于清理控制器占用的资源。

下面是一个使用`GetxController`生命周期方法的例子：

```dart
class MyController extends GetxController {
  @override
  void onInit() {
    super.onInit();
    print("onInit called");
    fetchData();
  }

  @override
  void onReady() {
    super.onReady();
    print("onReady called");
  }

  @override
  void onClose() {
    super.onClose();
    print("onClose called");
  }

  void fetchData() async {
    await Future.delayed(Duration(seconds: 2));
    print("data fetched");
  }
}
```

在`initState`中使用`MyController`，琢磨控制台将打印：

调用。

```dart
class MyBuilderController extends GetxController{
  @override
  void onDelete() {
    print("Controller has been deleted");
    super.onDelete();
  }

  @override
  void onDetached() {
    print("Controller not found");
    super.onDetached();
  }
}
```
需要注意的是，当使用GetXController的时候，一定要记得调用super，以防止内存泄漏。


#### GetxController三种使用方式:

1、应用程序入口设置

```dart
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      title: "GetX",
      home: GetXControllerExample(),
    );
  }
}
```

2、定义控制器继承自GetxController

```dart
class MyController extends GetxController {}
```

3、实例化控制器并使用

```dart
class GetXControllerExample extends StatelessWidget {
    MyController myController = Get.put(MyController());
}

```

#### GetxController UniqueID

在开发的过程中会碰到一种情况，就是多个地方引用了同一个属性，但我只想单独更新某一个地方，那么就可以用`UniqueID`来进行区分。

```dart
class CountController extends GetxController {
  var count = 0;

  void increment() {
    count++;
    update(['count']);
  }
}

class GetXControllerUniqueIDExample extends StatelessWidget {

  CountController countController = Get.put(CountController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            GetBuilder<CountController>(
              builder: (controller) {
                return Text(
                  "值为: ${controller.count}",
                  style: TextStyle(color: Colors.red, fontSize: 30),
                );
              },
            ),
            GetBuilder<CountController>(
              id: 'count',
              builder: (controller) {
                return Text(
                  "值为: ${controller.count}",
                  style: TextStyle(color: Colors.green, fontSize: 30),
                );
              },
            ),
            SizedBox(height: 20,),
            ElevatedButton(
              onPressed: () => countController.increment(),
              child: Text("增加"))
          ],
        ),
      ),
    );
  }
}
```
如上示例，只有ID为count的节点值才会更新。