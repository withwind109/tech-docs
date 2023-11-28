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
user.update((value) {  value?.name = "123";});
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

一个使用列表的示例：


### 2、路由管理：
  GetX 对Flutter的路由进行了封装，提供了更易用的API，比如通过Get.to进行页面跳转，不需要上下文context。

   ```dart
  //跳转到NextPage页面
  Get.to(NextPage());
  ```
  其中，`NextPage()`就是你想跳转的目标页面的构造函数。使用这种结构，你不需要提供任何上下文—`BuildContext`对象。

  此外，GetX 也可以提供一些更复杂的页面跳转操作。例如，可以等待进入下一页面之后返回一个结果：
```dart
var data = await Get.to(NextPage());
```

还可以使用命名路由：
```dart
Get.toNamed("/Next");
```
在跳转前，需要在GetMaterialApp中定义好名称及对应的页面：
```dart
void main() { 
   GetMaterialApp( 
     getPages: [ 
       GetPage(name: '/next', page: ()=> NextPage()),
     ], 
     home: FirstPage(),
   ); 
}
```

你还可以使用Get.back() 跳转到上一页：
```dart
Get.back();
```

使用Get.off() 关闭所有旧页面并跳转到新页面：
```dart
Get.off(NextPage());
```

使用Get.offAll() 清除所有的页面堆栈并跳转到新页面：
```dart
Get.offAll(NextPage());
```

传递参数至下一页面：
```dart
Get.to(NextPage(), arguments:"Hello GetX");
```

在下一页面中获取参数：
```dart
class NextPage extends StatelessWidget {
  @override
  Widget build(context) {
    final String text = Get.arguments;
    ...
  }
}
```

其中每一个步骤都不需要BuildContext对象，所以无论你在哪里，你都可以使用这些功能而不会有任何影响。 

### 3、依赖管理：

GetX对依赖管理提供了很好的解决方案，可以非常方便地获取数据或者对象。 依赖管理主要是通过Get.put(), Get.find()和Get.lazyPut()等方法来实现的。第一次使用某个 Controller 时需要使用`Get.put()`进行初始化，后续再使用同一个 Controller 就不需要再进行初始化，直接通过`Get.find()`来获取实例就可以了。


1、Get.put():
这是一个类注入的实例，你可以在任何地方注入你需要的类，创建一个新的实例。
```dart
void main() {
  Get.put(Controller());
  runApp(App());
}
```

2、Get.find():
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

3、Get.lazyPut():
lazyPut() 方法和 put() 方法含义相同，但和 put() 方法不同点在于，当使用 lazyPut() 时，只有在你真正用到的时候才会初始化这个类的实例。

```dart
void main() {
  Get.lazyPut(() => Controller());
  runApp(App());
}
```

这样就可以将需要使用的实例延迟初始化，当真正使用的时候再加载，以提高性能。 


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