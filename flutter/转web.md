首先运行以下命令以确保Flutter SDK是最新的：(不必要)

```sh
flutter upgrade
```

然后，确认你有Web支持：

```sh
flutter doctor
```

如果还没启用Web支持，则需要运行下面的命令：

```sh
flutter config --enable-web
```

创建一个新的Flutter项目或者使用已有的Flutter项目，然后在项目目录内执行以下命令：

```sh
flutter create .
```

确保Web为目标平台，你可以通过运行以下命令来列出所有可用的设备，以及你可以切换的平台：

```sh
flutter devices
```

构建Web版本的应用，执行以下命令：
   
```sh
flutter build web
```

这将会在你的项目目录下的build/web文件夹内生成一个Web应用。

一些常见的参数及其作用：

1. `--web-renderer html` 或 `--web-renderer canvaskit`: 用于指定 web 渲染器。`html` 使用更加轻量的 DOM 渲染器，适用于具有较小体积的应用；而 `canvaskit` 使用基于 WebGL 的渲染器，支持更好的图形性能和字型渲染质量。

2. `--release`: 构建一个针对生产环境的 release 版本，这会启用混淆、删除未使用代码等优化措施。

3. `--profile`: 构建一个针对性能调优的 profile 版本，可以进行一些性能测试。

4. `--debug`: 构建一个带有调试信息的版本，便于开发调试。

5. `--dart-define=<key>=<value>`: 允许您传入任意的定义到 Dart 代码中，可以用于配置环境变量或者控制应用在不同环境下的行为。

6. `--base-href`: 为应用程序指定一个基本的 URL 路由前缀。这在部署到子路径时特别有用。

7. `--csp`: 生成符合内容安全策略(CSP)的代码。这是安全相关的，有助于防止 XSS 攻击。

8. `--pwa-strategy=<strategy>`: 定义 Progressive Web App (PWA) 的缓存或更新策略。

9. `--source-maps`: 是否生成源码映射文件（source maps），有助于调试生产环境的 JavaScript 代码。

10. `--verbose`: 可以提供更详细的构建输出信息，便于排查构建出现的问题。

11. `--build-number=<number>`: 指定构建的版本号。

12. `--build-name=<name>`: 指定构建的版本名称。

https://skyui.cn/blog/flutter-web-rendering-performance.html

#### 树摇图标编译报错：

Font asset "CupertinoIcons.ttf" was tree-shaken, reducing it from 283452 to 1272 bytes (99.6% reduction). Tree-shaking can be disabled by providing the --no-tree-shake-icons flag
when building your app.
Font asset "MaterialIcons-Regular.otf" was tree-shaken, reducing it from 1645184 to 8912 bytes (99.5% reduction). Tree-shaking can be disabled by providing the
--no-tree-shake-icons flag when building your app.
Target web_release_bundle failed: IconTreeShakerException: Font subsetting failed with exit code 255.

To disable icon tree shaking, pass --no-tree-shake-icons to the requested flutter build command

原因是如果使用了有树摇图标资源的基础库如cupertino_icons，就会报错。
解决：
flutter build web --no-tree-shake-icons


#### 字体方格乱码问题：

git的issues：https://github.com/flutter/flutter/issues/73628

原因是基于canvaskit渲染的HTML页面会有问题导致的乱码，canvaskit已经在0.24修复了这个问题，不过需要升级flutter来升级。如果不能通过切换到HTML渲染器来解决这个问题。

```sh
flutter build web --web-renderer html
```

 Flutter Web 提供了两种渲染器供开发者选择：HTML 渲染器（也称为 DomCanvas）和 CanvasKit 渲染器。这两种渲染器在渲染 Flutter Web 应用时采用了不同的方式来绘制 UI，它们各自有不同的优点和缺点。

 https://docs.flutter.dev/deployment/web
