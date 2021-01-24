# Hybrid 原理

混合 App 原理

<img src="./assets/Hybrid 的 JSBridge.png">

实现 Hybrid App （混合应用）的关键在于实现 JSBridge。

JSBridge 主要是给 JS 提供调用 Native 功能的接口，让混合开发中的 JS 能够调用 Native 的能力（获取地理位置、拍照等）。

JSBridge 的核心是构建 JS 和 Native 之间 `双向通信的通道`，`双向通信的通道`指：

- JS 向 Native 端发送消息：调用 Native 的功能（如获取地理位置、拍照等）
- Native 向 JS 端发送消息：执行 JS 在 Window 注册的回调

## 1. 实现原理

### 1.1 JS 调用 Native

JS 调用 Native 有两种实现方式：

- Native 注入 API
- 拦截 URL SCHEME

#### 1.1.1 Native 注入 API

##### IOS 注入 API

1. 对于 iOS 的 UIWebView：

```objective-c
JSContext *context = [uiWebView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];

context[@"postBridgeMessage"] = ^(NSArray<NSArray *> *calls) {
    // Native 逻辑
};
```

前端调用：

```js
window.postBridgeMessage(message);
```

2. 对于 iOS 的 WKWebView：

```objective-c
@interface WKWebVIewVC ()<WKScriptMessageHandler>

@implementation WKWebVIewVC

- (void)viewDidLoad {
    [super viewDidLoad];

    WKWebViewConfiguration* configuration = [[WKWebViewConfiguration alloc] init];
    configuration.userContentController = [[WKUserContentController alloc] init];
    WKUserContentController *userCC = configuration.userContentController;
    // 注入对象，前端调用其方法时，Native 可以捕获到
    [userCC addScriptMessageHandler:self name:@"nativeBridge"];

    WKWebView wkWebView = [[WKWebView alloc] initWithFrame:self.view.frame configuration:configuration];

    // TODO 显示 WebView
}

- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
    if ([message.name isEqualToString:@"nativeBridge"]) {
        NSLog(@"前端传递的数据 %@: ",message.body);
        // Native 逻辑
    }
}
```

前端调用：

```js
window.webkit.messageHandlers.nativeBridge.postMessage(message);
```

##### Android 注入 API

因为 Android 注入 API 有兼容性问题，所以 Android 可以通过拦截 `window.prompt`，分析参数执行相应的方法来实现 JS 调用 Native。

#### 1.1.2 拦截 URL SCHEME

1. 什么是 URL SCHEME？

   URL SCHEME 是一种类似于 url 的链接，是为了方便 app 直接互相调用设计的，和普通的 url 近似。主要区别是 protocol 和 host 是自定义的，例如: xmly-intl://open?msg_type=2。其中 protocol 是 xmly-intl，host 则是 open。

2. 拦截 URL SCHEME 的流程

- Web 端通过某种方式（iframe.src）发送 URL SCHEME 请求
- Native 拦截到请求，并根据 URL SCHEME 进行相关的操作

H5 使用 iframe 打开一个 scheme 的代码实现：

```typescript
/**
 * wake up app by schema url or go to download url
 * @param {string} schemaUrl schema url
 * @param {string} downLoadUrl download url
 * @return {void}
 */
export function download(schemaUrl: string, downLoadUrl: string): void {
  let ifr = document.createElement('iframe');
  ifr.src = schemaUrl;
  ifr.style.display = 'none';
  document.body.appendChild(ifr);
  checkOpen(function (opened: number): false | void {
    if (opened === 1) {
      window.location.href = downLoadUrl; // 如果跳转失败，则使用 location.href 弥补
    } else {
      return false;
    }
  });
  setTimeout(() => {
    document.body.removeChild(ifr);
  }, 2000);
}

/**
 * Check if it opens successfully
 * @param {Function} callback
 * @return {void}
 */
function checkOpen(cbFn: Function): void {
  let startTime = +new Date();
  let count = 0;
  // 启动间隔20ms运行的定时器，并检测累计消耗时间是否超过2000ms，超过则结束
  const openTimer = setInterval(function () {
    count++;
    let expendTime = +new Date() - startTime;
    if (count >= 100 || expendTime >= 2000) {
      clearInterval(openTimer);
      // 0表示跳转APP成功,1表示失败
      if (
        document.hidden ||
        (document as any).mozHidden ||
        (document as any).webkitHidden ||
        (document as any).msHidden
      ) {
        cbFn(0);
      } else {
        cbFn(1);
      }
    }
  }, 20);
}
```

### 1.2 Native 调用 JS

Native 调用 JavaScript 比较简单：拼接 JavaScript 字符串，从外部调用 JavaScript 中的方法即可。

## 2. JSBridge 如何引入

见文章开头的图
