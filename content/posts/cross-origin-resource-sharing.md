+++
title = '跨域资源共享（CORS）'
date = 2024-07-02T01:40:37+08:00
tags = ['Web', 'CORS', '浏览器', '跨域']
draft = false
+++

在 Web 开发中，跨域资源共享（Cross-Origin Resource Sharing，CORS）是一种机制，它允许服务器在响应中设置 HTTP 头，以允许浏览器访问不同域的资源。CORS 是一个重要的安全特性，它可以帮助防止跨站点请求伪造（Cross-Site Request Forgery，CSRF）攻击。

## 跨域请求

在 Web 开发中，跨域请求是指浏览器从一个域（origin）向另一个域（origin）发起请求。一个域是由协议（scheme）、主机（host）和端口（port）组成的。如果两个请求的域不同，就称为跨域请求。

跨域请求是一种常见的情况，比如一个网页中包含了来自不同域的资源，或者一个网页中包含了来自不同域的第三方脚本。在这种情况下，浏览器会根据 [同源策略][]（Same-Origin Policy）来限制跨域请求。

## 同源策略

同源策略是一种安全策略，它限制了一个域的脚本只能访问同一个域的资源。同源策略可以防止恶意网站窃取用户信息，保护用户的隐私。

同源策略的限制包括：

- Cookie、LocalStorage 和 IndexDB 等存储在浏览器中的数据只能被同一个域的脚本访问。
- XMLHttpRequest 和 Fetch 等网络请求只能向同一个域发起。
- DOM 元素只能被同一个域的脚本访问。

## CORS

CORS 是一种为安全地处理跨域请求而设计的机制。CORS 通过在响应中设置 HTTP 头来告诉浏览器允许哪些域访问资源。CORS 机制允许服务器在响应中设置 `Access-Control-Allow-Origin` 头，以允许浏览器访问不同域的资源。

CORS 机制包括两种请求：

- 简单请求（Simple Request）：满足一定条件的请求，浏览器会自动处理。
- 预检请求（Preflight Request）：不满足简单请求条件的请求，浏览器会先发送一个预检请求，然后再发送实际请求。

### 简单请求

满足以下条件的请求是简单请求：

- 请求方法是 `GET`、`HEAD` 或 `POST`。
- 请求头只包含以下字段：`Accept`、`Accept-Language`、`Content-Language`、`Content-Type`（只限于 `application/x-www-form-urlencoded`、`multipart/form-data` 或 `text/plain`）。

对于简单请求，服务器在响应中设置 `Access-Control-Allow-Origin` 头，以允许浏览器访问资源。

### 预检请求

不满足简单请求条件的请求是预检请求。浏览器会先发送一个预检请求，然后再发送实际请求。

预检请求包括以下步骤：

1. 浏览器发送一个 `OPTIONS` 请求，包含以下头部信息：
   - `Access-Control-Request-Method`：请求方法。
   - `Access-Control-Request-Headers`：请求头。

2. 服务器在响应中设置以下头部信息：
    - `Access-Control-Allow-Origin`：允许的域。
    - `Access-Control-Allow-Methods`：允许的方法。
    - `Access-Control-Allow-Headers`：允许的头部。
    - `Access-Control-Max-Age`：预检请求的有效期。

3. 浏览器根据服务器的响应决定是否发送实际请求。

## 应用场景

CORS 机制可以用于以下场景：

- 跨域 AJAX 请求：允许浏览器向不同域发起 AJAX 请求。
- 跨域字体：允许浏览器加载不同域的字体。
- 跨域 WebGL 纹理：允许浏览器加载不同域的 WebGL 纹理。
- 跨域图片，视频: 在画布上绘制来自不同域的图片或视频。
- 跨域 CSS Shapes: 允许浏览器使用来自不同域的图片来定义 CSS Shapes。

## 参考资料

1. [MDN Web Docs: Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
2. [W3C: Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/)
