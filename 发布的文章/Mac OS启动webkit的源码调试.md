<!--
 * @Author: cdtangchao
 * @Desc: 添加注释
 * @Date: 2022-12-05 10:57:02
 * @LastEditors: cdtangchao
 * @LastEditTime: 2022-12-05 10:58:19
-->
# 概念
## 关于webkit引擎内核：
```
WebKit is an engine, not a browser.
WebKit是一个引擎，而不是一个浏览器。
```
在WebKit首先苹果声明的就是WebKit是一个web引擎，它是苹果自己的引擎，使用在了苹果的Safari浏览器、邮件、appStore等软件中。其核心包含了WebCore排版引擎、JavaScriptCore的渲染引擎、WebKit平台等。

Chrome使用的是Webkit还是Blink？

Blink is a fork of the WebCore component of WebKit, which was originally a fork of the KHTML and KJS libraries from KDE. It is used in Chrome starting at version 28, Microsoft Edge starting at version 79, Opera (15+), Vivaldi, Brave, Amazon Silk and other Chromium-based browsers and frameworks.

IOS上的Chrome使用的是什么内核？

Chrome for iOS continues to use WebKit because Apple requires that web browsers on that platform must do so.

这个应该就是导致我们的前端js代码在android或者电脑端执行结果不一致的问题，比如

转《WebKit技术内幕》一书中的架构图：
![0a0cf63f-b74f-473e-b62c-0baa5506dffd.jpg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/afd9e4ce8d8244c9bff365fb72b19f3f~tplv-k3u1fbpfcp-watermark.image?)

WebKit和WebKit2的区别就是，如果我们做Mac和IOS开发中初始化一个UIWebView对象，则使用的WebKit，如果初始化的是一个WKWebView对象，则使用的WebKit2。

# 下载和编译：

在Mac电脑需要安装Xcode，等待xcode安装Xcode Command Line。

`注意：编译完成后的仓库体积在60G加，首先要确定自己的硬盘的存储空间是否足够。`

首先在github中clone代码仓库到本地：
[https://github.com/WebKit/WebKit](https://github.com/WebKit/WebKit)

然后在仓库目录中执行：
```
Tools/Scripts/set-webkit-configuration --debug
Tools/Scripts/build-webkit --debug
```
然后根据电脑配置不同，大概编译时间在半个小时到一个小时不等。

编译完成后进入仓库目录，点击WebKit.xcworkspace的Xcode项目空间的文件就可以打开WebKit的内核工程。

关于build location的修改：

<image src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32310fcc3a67484182ee8e4769b80506~tplv-k3u1fbpfcp-watermark.image" width="300"/>


# 工程目录

WebKit的项目目录如下：

![1669896938800.jpg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/45ccd642f8724a6fa926b6a337eb1f1c~tplv-k3u1fbpfcp-watermark.image?)

几个比较重要的工程：  
`bmalloc：`C++项目，实现动态内存分配，该库提供了一个成为IsoHeap的重要安全特性，它将每种类型的对象隔离到它自己的页面中，以防止对释放后使用的类型混淆攻击；   
`WTF:`补充C++标准模板库外的自定义模板库，包含常见的容器类，如Vector、HashMap(无序)、HashSet等等；  
`JavaScriptCore：`C++项目，苹果自己的jsc引擎，还有v8、quickJS、facebook的Hermes；  
`ANGLE：`Almost Native Graphics Layer Engine，在 Direct X 9.0c API 的基础上实现一层 OpenGL ES 2.0 API中 的 Web GL 子集接口；  
`libwebrtc:`实时音视频库；  
`WebGPU:`浏览器的图形API;  
`WebCore:`排版引擎，实现了HTML、XML和CSS解析器，资源加载等，C++实现；
`WebInspectorUI：`浏览器的调试和性能开发者工具实现，js开发实现，通过上述提到的jscore来运行；  
`WebKitLegacy：`原有UIWebView对象的实现，已经彻底的过期，使用会导致app被苹果拒审；  
`WebKit:`即WKWebView对象，实现了WebKit的多进程架构-UI进程、WebContent进程、NetWorking进程；  
`DumpReaderTree:`页面渲染树的测试工具；
`MiniBrowser:`一个简单的测试用的浏览器，object-c实现；  
`MobileMiniBrowser:`一个简单的测试用的移动端浏览器，object-c实现； 

然后编译的target选择MiniBrowser，等待编译完成后，会启动一个简单的浏览器：

<image src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2d8a9676ef424ec5861c835bf8446661~tplv-k3u1fbpfcp-watermark.image?" width="400"/>

MobileMiniBrowser工程中，核心功能是通过WK1BrowserWindowController实现UIWebView的加载和显示，WK2BrowserWindowController实现WKWebView的加载和显示，它们通过共用的基类BrowserWindowController来实现浏览器Window的UI展示。

启动后，在Xcode的左侧面的debug nav面板中就可以通过进程信息看到UIWebView和WKWebVIew的区别：



<!-- 参考：https://chromium.googlesource.com/external/github.com/WebKit/webkit/+/refs/tags/Safari-612.1.10.1/Introduction.md-->








