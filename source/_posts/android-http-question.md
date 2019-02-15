---
title: android客户端页面 请求超时自动重发
date: 2019/02/15 17:42:36
tags: question
---

## 原因
原因如下：由于设置了链接与获取数据的超时时间，客户端在发送数据之后，检测到可能并没有发送成功到后端，这个时候http底层会自动重发请求（注意是Http底层，所以应用端不会知道发送了多次请求）。如果应用端自动重发了多次请求，后端也回复了多次请求，但是前段仅仅会只回复1次请求。


## 解决方案

1. 所以为了解决这个问题，只要在DefaultHttpClient设置如下代码即可解决：
```
    defaultHttpClient.setHttpRequestRetryHandler(new DefaultHttpRequestRetryHandler(0, false));

```

2. h5端解法方式。主要是解决post 方法返回结果，第一次和第二次不同。采用对结果使用另一个接口查询的方式解决。

```javascript
    Api1().then((res) => {
      // 安卓机对请求时间过长的请求会自动请求第二次，导致签署结果无法正确返回
       // 以下为兼容代码，谨慎重构、删除
    if (isAndroid()) {
        Api2().then(() => {
            ...
        }).catch(() => {
            ...
        });
    } else {
        ...
    }}
```


