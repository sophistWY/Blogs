---
title: WKWebView
date: 2021-4-18 11:31:43
tags:
  - 方法总结
categories:
  - iOS
---

# WKWebView

<!--more-->


### 一.WKWebViewConfiguration

#### 1.WKPreferences

当前webview的相关设置

```objective-c
        
WKPreferences *preferences = [[WKPreferences alloc] init];
// 最小字体
preferences.minimumFontSize = 100.0f;        
// 是否支持 web 调用 window.open() 打开新的空白窗口 (js自执行)
preferences.javaScriptCanOpenWindowsAutomatically = YES;
// 警告是否应该显示疑似欺诈内容，如网络钓鱼或恶意软件        
preferences.fraudulentWebsiteWarningEnabled = NO;
```



#### 2.WKUserContentController

用户脚本

```objective-c
// 已经注入的用户脚本
@property (nonatomic, readonly, copy) NSArray<WKUserScript *> *userScripts;

// 利用 window.webkit.messageHandlers.<name>.postMessage(), 配合代理直接进行通信
 - (void)addScriptMessageHandler:(id <WKScriptMessageHandler>)scriptMessageHandler name:(NSString *)name;

// 创建用户脚本，并选择注入时机和作用的Frame
- (instancetype)initWithSource:(NSString *)source injectionTime:(WKUserScriptInjectionTime)injectionTime forMainFrameOnly:(BOOL)forMainFrameOnly;


```



###   二.WKNavigationDelegate

hook整个webview加载过程中的生命周期，通过decisionHandler让用户决定拦截自行处理或是继续进行。

```objective-c
/// ① 根据WebView对于即将跳转的HTTP请求头信息和相关信息来决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler;

/// ② 页面开始加载
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)navigation;

/// ③ 根据客户端收到的服务器响应头以及response相关信息来决定是否可以跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler;

/// ④ 当内容开始返回时调用
- (void)webView:(WKWebView *)webView didCommitNavigation:(WKNavigation *)navigation;

/// ⑤ 页面加载完成之后调用
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation;

/// 页面加载失败时调用
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(null_unspecified WKNavigation *)navigation withError:(NSError *)error;

/// Mainframe加载发生错误时调用
- (void)webView:(WKWebView *)webView didFailNavigation:(WKNavigation *)navigation withError:(NSError *)error;

/// 服务重定向时之后调用
- (void)webView:(WKWebView *)webView didReceiveServerRedirectForProvisionalNavigation:(WKNavigation *)navigation;

/// 身份验证时调用,同样在block中需要传入用户身份凭证,
/// 1. 与使用自签名证书的服务器进行 HTTPS 连接。
/// 2. 进行更严格的服务器信任评估来加强安全性，如：通过使用 SSL Pinning 来防止中间人攻击。
- (void)webView:(WKWebView *)webView didReceiveAuthenticaqionChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential * _Nullable credential))completionHandler;

/// 进程被终止时调用，页面即将白屏 (iOS 9.0), eg:app内存耗尽~
- (void)webViewWebContentProcessDidTerminate:(WKWebView *)webView;
```



### 三.WKUIDelegate

代表网页呈现本地UI。

```objective-c
/// web 调用 alert()
- (void)webView:(WKWebView *)webView runJavaScriptAlertPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(void))completionHandler;

/// web 调用 confirm()
- (void)webView:(WKWebView *)webView runJavaScriptConfirmPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(BOOL))completionHandler;

/// web 调用 prompt()
- (void)webView:(WKWebView *)webView runJavaScriptTextInputPanelWithPrompt:(NSString *)prompt defaultText:(NSString *)defaultText initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(NSString * _Nullable))completionHandler;

/// 页面是弹出窗口处理 (subFrame)
- (WKWebView *)webView:(WKWebView *)webView createWebViewWithConfiguration:(WKWebViewConfiguration *)configuration forNavigationAction:(WKNavigationAction *)navigationAction windowFeatures:(WKWindowFeatures *)windowFeatures;

```



### 四.WKScriptMessageHandler

```objective-c
/// 监听js中 window.webkit.messageHandlers.<name>.postMessage() 方式传递过来的数据
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
  
}

```



### 五.缓存相关

#### 1.NSURLRequest 缓存

NSURLRequestUseProtocolCachePolicy策略：

![46788d50-fb95-48f2-8360-b8c2a4bf1648](/Users/yangwang/Desktop/46788d50-fb95-48f2-8360-b8c2a4bf1648.png)

http和https缓存的详细文档：

 https://imweb.io/topic/5795dcb6fb312541492eda8c

 [Cache In Http]( https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13 )

总结：

> 请求头中的三个字段 
>
> Cache-Control : 控制缓存的行为 (no-cahce 确认返回的响应是否更改，若响应体Etag未变化, 请求还是会失败返回304)
>
> If-Modified-Since: Last-Modified-value
>
> If-Match (If-No-Match) : 上次请求成功的Etag
>
> 响应体：
>
> Last-Modified：上一次修改时间
>
> Etag: 一个可以与资源关联的唯一id



#### 2.WKWebsiteDataStore

一个网站可能拥有的各种类型的数据缓存

```objective-c
// 沙盒目录: Library/Caches/${BundleId}/WebKit/... 
NSString *targetHost = self.webView.URL.host;
    //allWebsiteDataTypes 所有类型的web缓存
    NSSet *websiteDataTypes = [WKWebsiteDataStore allWebsiteDataTypes];
    // 清除指定域名的缓存
    [[WKWebsiteDataStore defaultDataStore] fetchDataRecordsOfTypes:websiteDataTypes completionHandler:^(NSArray<WKWebsiteDataRecord *> * _Nonnull dataRecords) {
        for (WKWebsiteDataRecord *dataRecord in dataRecords) {
            NSLog(@"缓存的域名：%@", dataRecord.displayName);
            if ([targetHost isEqualToString:dataRecord.displayName]) {
                [[WKWebsiteDataStore defaultDataStore] removeDataOfTypes:dataRecord.dataTypes forDataRecords:@[dataRecord] completionHandler:^{
                    
                }];
                break;
            }
        }
    }];
    NSDate *dateFrom = [NSDate dateWithTimeIntervalSince1970:0];
    [[WKWebsiteDataStore defaultDataStore] removeDataOfTypes:websiteDataTypes modifiedSince:dateFrom completionHandler:^{

    }];
```











