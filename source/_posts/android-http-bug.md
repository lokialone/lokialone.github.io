---
title: android 请求超时自动重发
date: 2019-02-18 01:28:46
tags: "bug"
---
## 现象
安卓机webview里的h5，post请求时间过长时，接口获得的数据是二次或者多次请求后的结果

## 原因
原因如下：由于设置了链接与获取数据的超时时间，客户端在发送数据之后，检测到可能并没有发送成功到后端，这个时候http底层会自动重发请求（注意是Http底层，所以应用端不会知道发送了多次请求）。如果应用端自动重发了多次请求，后端也回复了多次请求，但是前端仅仅会只回复1次请求。


## 解决方案

1. 所以为了解决这个问题，只要在DefaultHttpClient设置如下代码即可解决：
```
defaultHttpClient.setHttpRequestRetryHandler(new DefaultHttpRequestRetryHandler(0, false));

```

2. h5端解法方式。主要是解决post 方法返回结果，第一次和第二次不同。采用对结果使用另一个接口查询的方式解决。

```javascript
postSomething().then((res) => {
    // 安卓手机进行查询接口
    if (isAndroid()) {
        queryResult()
    } else {
        // 根据结果进行处理
    }
});
```