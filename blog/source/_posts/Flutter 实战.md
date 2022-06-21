---
title: Flutter 实战
date: 2022-01-10 14:50:13
tags: CSS
categories:
- Flutter
---



## Flutter 实战

<!--more-->

####  一.前置概念

**1. Flutter module** 

**概念：**

将Flutter添加到你的既有 iOS 应用中(利用CocoaPods)

**创建 :**

```shell
cd some/path/
flutter create --template module my_flutter
```

**2. 原生插件** **(Plugin packages)**

**概念：**

使用 Dart 编写的，按需使用 Java 或 Kotlin、Objective-C 或 Swift 分别在 Android 和/或 iOS 平台实现的 package。

**创建 :**

```shell
// 使用 -a 选项指定 Android 的语言，或使用 -i 选项指定 iOS 的语言
flutter create --org com.example --template=plugin --platforms=android,ios -a kotlin -i swift flutter_plugins
```

**3. 纯** **Dart** **库** **(Dart packages)** 

**概念：**

用 Dart 编写的传统 package，比如 [path](https://pub.flutter-io.cn/packages/path)。其中一些可能包含 Flutter 的特定功能，因此依赖于 Flutter 框架，其使用范围仅限于 Flutter，比如 [fluro](https://pub.flutter-io.cn/packages/fluro)。

**创建：**

```shell
flutter create --template=package flutter_packages
```

*** Tips：***

1. 你的应用将不能在模拟器上运行 Release 模式，因为 Flutter 还不支持将 Dart 代码编译成 x86/x86_64 ahead-of-time (AOT) 模式的二进制文件。

2. `Flutter.framework` 是 Flutter engine 的框架， `App.framework` 是你的 Dart 代码的编译产物。

3. [参考文档 >>](https://flutter.cn/docs/development/packages-and-plugins/developing-packages)

   

#### 二. 开始集成

##### 修改Podfile

1. 在 `Podfile` 中添加下面代码：

   ```ruby
   flutter_application_path = '${flutter module目录}'
   load File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')
   ```

2. 每个需要集成 Flutter 的 [Podfile target](https://guides.cocoapods.org/syntax/podfile.html#target)，执行 `install_all_flutter_pods(flutter_application_path)`：

   ```ruby
   target 'MyApp' do
     install_all_flutter_pods(flutter_application_path)
   end
   ```

3. 运行 `pod install`。

*** Tips：***

1. podhelper.rb 脚本会把你的 plugins， Flutter.framework，和 App.framework 集成到你的项目中
2. 运行一个集成flutter module的项目：

```shell
cd ${flutter module目录}
flutter pub upgrade
cd ${iOS工程目录}
pod install
```



#### 三. Flutter Engine

```swift
		lazy var flutterEngine = FlutterEngine(name: "flutter engine")

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // 启动引擎
        self.flutterEngine.run()
        // 注册插件
        GeneratedPluginRegistrant.register(with: self.flutterEngine);
        // 平台视图
        let factory = FLNativeViewFactory(messenger: self.flutterEngine.binaryMessenger)
        self.flutterEngine.registrar(forPlugin: "<plugin-name>")?.register(factory, withId: "<platform-view-type>")
        
        return true
    }
```

*** Tips：***

1.*** IDE推荐Android Studio***

2.在 `FlutterEngine` 上调用 `run`，默认将会调用你的 `lib/main.dart` 文件里的 `main()` 函数。



#### 四. Flutter和iOS原生交互

##### 1. Methods Channel

##### dart To iOS

```dart
// 测试 methodChannel  dart -> iOS
MethodChannel methodChannel = const MethodChannel('samples.flutter.dev');
Future<void> _getDeviceInfo() async {
  try {
    final String result = await methodChannel.invokeMethod('getDeviceInfo', {"key": "value"});
    _deviceInfo = result;
    debugPrint(result);
  } on PlatformException catch (e) {
    _deviceInfo = "Failed to get device info: '${e.message}'.";
  }
  setState(() {});
}
```



```swift
// iOS 端
lazy var methodChannel:FlutterMethodChannel = {
    return FlutterMethodChannel(name: "samples.flutter.dev",
                  binaryMessenger: self.flutterEngine.binaryMessenger);
  }()
  
  
func initChannel() {
    self.methodChannel.setMethodCallHandler({
      (call: FlutterMethodCall, result: @escaping FlutterResult) -> Void in
      // Note: this method is invoked on the UI thread.
      // Handle battery messages.
      if (call.method == "getDeviceInfo") {
        let device = UIDevice.current
        result(device.identifierForVendor?.uuidString)
      } else {
        result(FlutterMethodNotImplemented)
      }
    })
  }
```



##### iOS To dart

```swift
// iOS 端
 self.methodChannel.invokeMethod("callToDart", arguments: ["我是原生端":"呵呵🙂"]) { data in
      // Dart 返回的数据
      if let content = data as? String {
        let alert = UIAlertController(title: "Dart返回的数据", message: content, preferredStyle: UIAlertController.Style.alert)
        alert.addAction(UIAlertAction(title: "确定", style: UIAlertAction.Style.default, handler: { action in
           
        }))
        self.present(alert, animated: true, completion: nil)
      }
    }

```

```dart
// dart 端
MethodChannel methodChannel = const MethodChannel('samples.flutter.dev');

void _listenFromNative() {
  methodChannel.setMethodCallHandler(_fromNative);
}

Future<dynamic> _fromNative(MethodCall call) async {
  if (call.method == "callToDart") {
    debugPrint(call.arguments.toString());
    setState(() {
      _nativeData = call.arguments.toString();
    });
  } else {
    return FlutterError("FlutterMethodNotImplemented");
  }
  return {"FlutterKey": "呵呵🙂"}.toString();
}

```



##### 2.平台视图托管 (PlatformView)

集成平台视图（后称为平台视图）允许将原生视图嵌入到 Flutter 应用中，所以您可以通过 Dart 将变换、裁剪和不透明度等效果应用到原生视图。（eg: Map,Webview ... ）

```dart
// Dart 端
Widget build(BuildContext context) {
  // This is used in the platform side to register the view.
  final String viewType = '<platform-view-type>';
  // Pass parameters to the platform side.
  final Map<String, dynamic> creationParams = <String, dynamic>{};

  return UiKitView(
    viewType: viewType,
    layoutDirection: TextDirection.ltr,
    creationParams: creationParams,
    creationParamsCodec: const StandardMessageCodec(),
  );
}
```

```swift
// iOS 端
class FLNativeViewFactory: NSObject, FlutterPlatformViewFactory {
  private var messenger: FlutterBinaryMessenger

  init(messenger: FlutterBinaryMessenger) {
    self.messenger = messenger
    super.init()
  }

  func create(
    withFrame frame: CGRect,
    viewIdentifier viewId: Int64,
    arguments args: Any?
  ) -> FlutterPlatformView {
    return FLNativeView(
      frame: frame,
      viewIdentifier: viewId,
      arguments: args,
      binaryMessenger: messenger)
  }
}

class FLNativeView: NSObject, FlutterPlatformView {
  private var _view: UIView

  init(
    frame: CGRect,
    viewIdentifier viewId: Int64,
    arguments args: Any?,
    binaryMessenger messenger: FlutterBinaryMessenger?
  ) {
    _view = UIView()
    super.init()
    // iOS views can be created here
    createNativeView(view: _view)
  }

  func view() -> UIView {
    return _view
  }

  func createNativeView(view _view: UIView){
    _view.backgroundColor = UIColor.gray
    let nativeLabel = UILabel()
    nativeLabel.text = "Native text from iOS"
    nativeLabel.textColor = UIColor.cyan
    nativeLabel.textAlignment = .center
    nativeLabel.frame = CGRect(x: 0, y: 20, width: 200, height:100)
    _view.addSubview(nativeLabel)
  }
}

// 注册平台视图
  lazy var flutterEngine = FlutterEngine(name: "flutter engine")
  let factory = FLNativeViewFactory(messenger: self.flutterEngine.binaryMessenger)
  self.flutterEngine.registrar(forPlugin: "<plugin-name>")?.register(factory, withId: "<platform-view-type>")

```

##### 3.Event Channel

```swift
// swift
public class EventChannelDemo:NSObject, FlutterStreamHandler{
    
    var channel:FlutterEventChannel?
    var count =  0
    var events:FlutterEventSink?
    
    public override init() {
        super.init()
    }
    
    convenience init(messenger: FlutterBinaryMessenger) {
        self.init()
        channel = FlutterEventChannel(name: "samples.flutter.dev.EventChannel", binaryMessenger: messenger)
        channel?.setStreamHandler(self)
        startTimer()
    }
    
    func startTimer() {
        let timer = Timer.scheduledTimer(timeInterval:1, target: self, selector:#selector(self.tickDown),userInfo:nil,repeats: true)
    }
    @objc func tickDown(){
        count += 1
        let args = ["count":count]
        if(events != nil){
            events!(args)
        }
    }
    
    public func onListen(withArguments arguments: Any?, eventSink events: @escaping FlutterEventSink) -> FlutterError? {
        self.events = events
        return nil;
    }
    
    public func onCancel(withArguments arguments: Any?) -> FlutterError? {
        self.events = nil
        return nil;
    }
}
```

```dart
// Dart 
this.eventChannel = EventChannel("samples.flutter.dev.EventChannel");
this.eventChannel.receiveBroadcastStream().listen((event) {})
```

*** Tips：***

1. [平台通道文档>>](https://flutter.cn/docs/development/platform-integration/platform-channels?tab=type-mappings-obj-c-tab)

2. [平台视图文档>>](https://flutter.cn/docs/development/platform-integration/platform-views)

   

####  五. 包体积增长和性能测试

在 iOS 平台上，跟据 App Store Connect 的数据，同一应用的发布 IPA 在 iPhone X 上的下载文件体积为 10.9 MB。 IPA 比 APK 大，主要是因为 Apple 加密了 IPA 中的二进制文件，使得压缩效率降低。（可以查看 [iOS App Store Specific Considerations](https://developer.apple.com/library/archive/qa/qa1795/_index.html#//apple_ref/doc/uid/DTS40014195-CH1-APP_STORE_CONSIDERATIONS) 中 [QA1795](https://developer.apple.com/library/archive/qa/qa1795/_index.html) 关于加密的部分）

***Tips：***

[包体积优化>>](https://zhuanlan.zhihu.com/p/97244622)

[关于性能>>](https://flutter.cn/docs/perf) 

#### 六. 适用业务场景

几乎95%以上业务都能使用Flutter开发，即使flutter无法实现也能利用platformView进行原生视图托管实现。

#### 七. 相关资料

[dart语法>>]( https://www.dartcn.com/guides/language/language-tour)

[flutter官网>>](https://flutter.cn)

[组件库>>](https://flutter.cn/docs/reference/widgets)

[渲染原理>>](https://flutter.cn/docs/resources/architectural-overview)

*** 翻译(二手)资料：***

[中文入门教程>>]( https://book.flutterchina.club)

[中文组件库>>]( http://laomengit.com/)







