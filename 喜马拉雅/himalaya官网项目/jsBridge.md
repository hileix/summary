# 5 分钟快速了解 JSBridge

## 什么是 JSBridge ？

​ 简单来说，JSBridge 是一种 JS 实现的 Bridge，连接着桥两端的 Native 和 H5。它在 APP 内方便地让 H5 调用 Native、 Native 调用 H5，是双向通信的通道。在移动互联网盛行的年代，这种方式应用十分广泛。

## 为什么要使用 JSBridge？

首先，我们来看一张图。

![image-20200308130955016](https://note.youdao.com/yws/api/personal/file/WEB98bf9c22e77ce60ed97022917682ab8b?method=download&shareKey=e1ef6be32343253e6c2d9eec34708005)

​

​ 从上图可以明显的发现，H5 和 Native 在各方面各有利弊，是一种互补关系。H5 的缺点，借助 Native 可以解决， Native 的缺点，借助 H5 能解决。除此之外，有些业务场景不得不使用 H5，比如支付页面，为了避免 ios 审核被拒的风险，一般 app 内的支付我们都用 H5 来承载，否则我们只能使用比如 IAP（In-App Purchase）这样的方式。

​ 当在 app 内使用 H5 来承载某些功能时，H5 就不可避免的要与 Native 进行通信，调用 Native 的能力，将某些状态、结果通知到 Native，有时候 Native 也要将一些状态信息反馈给 H5，所以我们需要使用 `JSBridge` 来实现 H5 与 Native 的双向通信。

## JSBridge 的原理

JavaScript 调用 Native 的方式，主要有两种：**注入 API** 和 **拦截 URL SCHEME**

- **注入 API**

![image-20200308132148962](https://note.youdao.com/yws/api/personal/file/WEBafff1aaf8b0aa61fb7bd42731745058b?method=download&shareKey=3b74c6accc7d671757ae1355fd7a27c3)

从上图可以发现，

**H5 调用 Native 的过程： **

​ ios: ios 端将 `callNative.postMessage` 绑定在 `window` 上，H5 直接调用这个原生接收方法，并且将回调函数 注册到 `window` 上。

​ android: H5 直接调用 `window.prompt` 这个原生方法，并且将回调函数注册到 `window` 上。

**Native 调用 H5 的过程：**

​ ios: 收到来自 H5 的调用，分析执行相应的方法，调用 H5 注册到 `window` 上的回调，将结果返回。

​ android: 拦截 `window.prompt`，分析执行相应的方法，调用 H5 注册到 `window` 上的回调，将结果返回。

（备注：Android 注入对象的方式有兼容性问题）

**总结:**

以上，从 `h5` 到 `Native` , 再从 `Native` 到 `h5`的过程，是不是和 `jsonp`的原理一样。 不论是 H5 调用 Native，还是 Native 调用 H5，其实质都是将方法注入到 `window` 上，让 H5 调用时，直接执行相应的 Native 代码逻辑，达到 H5 调用 Native 的目的。反过来 Native 调用 H5 亦是如此。

`window` 是通信的纽带。

- **拦截 URL SCHEME**

简单的说，拦截 URL SCHEME 就是：Web 端通过请求地址的方式（例如 iframe.src 或者 window.location.href）发送 URL Scheme 请求，之后 Native 拦截到请求并根据 URL SCHEME（包括所带的参数）进行响应。

```js
xmly-intl://open?msg_type=2&track_id=${trackId}&album_id=${albumId}
```

比如上面这个 scheme 会打开 app 的某个原生页面，这里 `msg_type` 参数决定了打开哪个页面，`track_id`、`album_id` 是这个页面需要的参数。

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

除了 ` iframe` ，还可以使用 a 标签和 `window.location` 的方式来打开 APP 的页面。

想进一步了解唤起方式的差异，[点我](https://juejin.im/post/5b7efb2ee51d45388b6af96c)

#### Mix（JSBridge） 的实现

`mix` 是我们海外 H5 与 Native 交互的 jsbridge，下面我们一起来看看 `mix` 的实现过程是怎样的。

- API 的注入（H5 与 Native 双向通信）

`mix` 的核心是下面这段代码，几乎所有的方法底层都调用了`invokeApp`这个方法。

```typescript
/**
  * 底层调用native方法
  * @param action
  * @param service
  * @param args
  * @param callBack
	*/
invokeApp (action: string, service: string, args?: any, callBack?: Function) {
  if (!envInfo[window._mix_.env as Env].inApp) return
  let callbackName = bridge.registCallback(callBack)
  let paramsString = bridge.cmdFormatter(action, service, args, callbackName )
  if(envInfo[window._mix_.env as Env].deviceType === 'ios') {
    window.webkit.messageHandlers.callNative.postMessage(paramsString)
  } else {
    window.prompt('callNative', paramsString);
  }
}
```

分析 invokeApp：

1. 注册回调函数（ window.nativeCallBack[callbackName]）

2. 参数格式化（约定安卓和 ios 使采用相同的指令格式）

3. 根据设备的不同, 调用不同的方法来，传入参数，实现与 native 交互

invokeApp 的使用:

```typescript
/**
 * 播放声音列表
 * @param params
 */
export const playEpisodes = (params: IPlayEpisodesParams) => {
  let action = 'playEpisodes';
  let service = 'player';
  let args: IPlayEpisodesParams = params;
  if (envInfo[window._mix_.env as Env].inApp) {
    bridge.invokeApp(action, service, args); // 完成 API 的注入
  }
};
```

- 拦截 scheme（打开 Native 的某个页面）

```typescript
let Scheme = {
  /** scheme 前缀 */
  prefix: window._mix_.appPrefix,
  open(params?: { [key: string]: string }) {
    let _params: string = this.getQuery(params);
    let url = `${this.prefix}://open${_params}`;
    this.loadScheme(url);
  },
  loadScheme(url: string) {
    window.location.href = url;
  },
  getQuery(params: { [key: string]: string }): string {
    if (!params) {
      return '';
    }
    let result = '';
    for (let key in params) {
      result += `&${key}=${params[key]}`;
    }
    return result.replace(/^&?/, '?');
  },
};
```

可以看出，最终其实就是跳转到一个 `scheme` url，`Native` 会拦截这个 url, 根据参数的不同，来决定打开哪个具体的 `Native` 页面。比如打开一个录音页：

```typescript
/**
  * 打开录音页
  * @param title
  */
openRecord (title?: string) {
  let params:any = {
    'msg_type': PageType.Record
  }
  title && (params['tag'] = title)
  Scheme.open(params)
}
```

`msg_type` 参数决定要打开什么页面。

```typescript
export enum PageType {
  /** 专辑页 */
  Show = '13',

  /** 声音页 */
  Episode = '11',

  /** 录音页 */
  Record = '68',

  /** 支付页 */
  Pay = '79',

  /** 搜索页 */
  Search = '73',

  /** 大播放页 */
  Play = '11',
  ...
}
```

`mix` 的实现基本就是这样，想要了解更多细节，前往: [HIFE/mix](http://gitlab.ximalaya.com/HIFE/mix)

## JSBridge 的使用

- 由 H5 引用

  采用本地引入 npm 包的方式进行调用，直接与 JavaScript 一起执行。

  与由 Native 端注入正好相反，它的优点在于：JavaScript 端可以确定 JSBridge 的存在，直接调用即可；缺点是：如果桥的实现方式有更改，JSBridge 需要兼容多版本的 Native Bridge 或者 Native Bridge 兼容多版本的 JSBridge。

- 由 Native 注入

  注入方式和 Native 调用 JavaScript 类似，直接执行桥的全部代码。

  它的优点在于：桥的版本很容易与 Native 保持一致，Native 端不用对不同版本的 JSBridge 进行兼容；与此同时，它的缺点是：注入时机不确定，需要实现注入失败后重试的机制，保证注入的成功率，同时 JavaScript 端在调用接口时，需要优先判断 JSBridge 是否已经注入成功。

  我们项目中采用的是第一种方式。

## 最后

了解了`JSBridge` 的原理后，现在回想，`JSBridge` 是一个很简单的东西，只是实现方式会各有不同，`JSBridge` 更多的是一种交互方式，是一种思想。

在这个移动端盛行的时代，处处都有 `JSBridge` 的身影，`JSBridge` 已成为很多应用必不可少的一部分。相信这篇文章，能让大家对 `JSBridge` 有一个初步的了解。
