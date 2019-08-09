<p align="center">
  <img src="https://cloud.githubusercontent.com/assets/835857/14581711/ba623018-0436-11e6-8fce-d2ccd4d379c9.gif">
</p>

# JavaScript Cookie [![Build Status](https://travis-ci.org/js-cookie/js-cookie.svg?branch=master)](https://travis-ci.org/js-cookie/js-cookie) [![Code Climate](https://codeclimate.com/github/js-cookie/js-cookie.svg)](https://codeclimate.com/github/js-cookie/js-cookie) [![jsDelivr Hits](https://data.jsdelivr.com/v1/package/npm/js-cookie/badge?style=rounded)](https://www.jsdelivr.com/package/npm/js-cookie)

一个简单，轻量级的JavaScript API，用于处理cookie。

* 适用于 [所有](https://saucelabs.com/u/js-cookie) 浏览器
* 接受 [任何](#encoding) 字符
* [Heavily](test) tested
* 没有依赖
* [非显式](#json) JSON 支持
* 支持 AMD/CommonJS
* 兼容 [RFC 6265](https://tools.ietf.org/html/rfc6265)
* Useful [Wiki](https://github.com/js-cookie/js-cookie/wiki)
* 可用 [自定义编码/解码](#converters)
* gzip压缩后仅 **~900 bytes**


## 构建状态 ([including active Pull Requests](https://github.com/js-cookie/js-cookie/issues/286))

[![Selenium Test Status](https://saucelabs.com/browser-matrix/js-cookie.svg)](https://saucelabs.com/u/js-cookie)

## 安装

### 直接下载

Download the script [here](https://github.com/js-cookie/js-cookie/blob/latest/src/js.cookie.js) and include it (unless you are packaging scripts somehow else):

```html
<script src="/path/to/js.cookie.js"></script>
```

Or include it via [jsDelivr CDN](https://www.jsdelivr.com/package/npm/js-cookie):

```html
<script src="https://cdn.jsdelivr.net/npm/js-cookie@2/src/js.cookie.min.js"></script>
```

**Do not include the script directly from GitHub (http://raw.github.com/...).** The file is being served as text/plain and as such being blocked
in Internet Explorer on Windows 7 for instance (because of the wrong MIME type). Bottom line: GitHub is not a CDN.

### Package Managers

JavaScript Cookie supports [npm](https://www.npmjs.com/package/js-cookie) and [Bower](http://bower.io/search/?q=js-cookie) under the name `js-cookie`.

#### NPM
```
  $ npm install js-cookie --save
```

### Module Loaders

JavaScript Cookie can also be loaded as an AMD or CommonJS module.

## 基本用法

创建一个在整个网站上有效的Cookie：

```javascript
Cookies.set('name', 'value');
```

创建一个从现在起7天后过期的cookie，在整个站点上有效：

```javascript
Cookies.set('name', 'value', { expires: 7 });
```

创建一个过期的cookie，对当前页面的路径有效：

```javascript
Cookies.set('name', 'value', { expires: 7, path: '' });
```

读取cookie：

```javascript
Cookies.get('name'); // => 'value'
Cookies.get('nothing'); // => undefined
```

读取所有可见的Cookie：

```javascript
Cookies.get(); // => { name: 'value' }
```

*Note: It is not possible to read a particular cookie by passing one of the cookie attributes (which may or may not
have been used when writing the cookie in question):*

```javascript
Cookies.get('foo', { domain: 'sub.example.com' }); // `domain` won't have any effect...!
```

The cookie with the name `foo` will only be available on `.get()` if it's visible from where the
code is called; the domain and/or path attribute will not have an effect when reading.

删除cookie：

```javascript
Cookies.remove('name');
```

Delete a cookie valid to the path of the current page:

```javascript
Cookies.set('name', 'value', { path: '' });
Cookies.remove('name'); // fail!
Cookies.remove('name', { path: '' }); // removed!
```

*IMPORTANT! When deleting a cookie and you're not relying on the [default attributes](#cookie-attributes), you must pass the exact same path and domain attributes that were used to set the cookie:*

```javascript
Cookies.remove('name', { path: '', domain: '.yourdomain.com' });
```

*Note: Removing a nonexistent cookie does not raise any exception nor return any value.*

## Namespace conflicts

If there is any danger of a conflict with the namespace `Cookies`, the `noConflict` method will allow you to define a new namespace and preserve the original one. This is especially useful when running the script on third party sites e.g. as part of a widget or SDK.

```javascript
// Assign the js-cookie api to a different variable and restore the original "window.Cookies"
var Cookies2 = Cookies.noConflict();
Cookies2.set('name', 'value');
```

*Note: The `.noConflict` method is not necessary when using AMD or CommonJS, thus it is not exposed in those environments.*

## JSON

js-cookie为cookie提供非显式的JSON存储。

创建cookie时，您可以传递数组或对象文字，而不是值中的字符串。 如果这样做，js-cookie将存储对象的字符串表示形式`JSON.stringify`:

```javascript
Cookies.set('name', { foo: 'bar' });
```

使用默认的`Cookies.get` api读取cookie时，您会得到存储在cookie中的字符串表示形式：

```javascript
Cookies.get('name'); // => '{"foo":"bar"}'
```

```javascript
Cookies.get(); // => { name: '{"foo":"bar"}' }
```

当使用`Cookies.getJSON` api读取cookie时，您会得到根据存储在cookie中的字符串的解析表示形式`JSON.parse`：

```javascript
Cookies.getJSON('name'); // => { foo: 'bar' }
```

```javascript
Cookies.getJSON(); // => { name: { foo: 'bar' } }
```

*注意: 为了支持 IE6-7 ([and IE 8 compatibility mode](http://stackoverflow.com/questions/4715373/json-object-undefined-in-internet-explorer-8)) 你需要包含 JSON-js polyfill: https://github.com/douglascrockford/JSON-js*

## 编码

This project is [RFC 6265](http://tools.ietf.org/html/rfc6265#section-4.1.1) compliant. All special characters that are not allowed in the cookie-name or cookie-value are encoded with each one's UTF-8 Hex equivalent using [percent-encoding](http://en.wikipedia.org/wiki/Percent-encoding).  
The only character in cookie-name or cookie-value that is allowed and still encoded is the percent `%` character, it is escaped in order to interpret percent input as literal.  
Please note that the default encoding/decoding strategy is meant to be interoperable [only between cookies that are read/written by js-cookie](https://github.com/js-cookie/js-cookie/pull/200#discussion_r63270778). To override the default encoding/decoding strategy you need to use a [converter](#converters).

*Note: According to [RFC 6265](https://tools.ietf.org/html/rfc6265#section-6.1), your cookies may get deleted if they are too big or there are too many cookies in the same domain, [more details here](https://github.com/js-cookie/js-cookie/wiki/Frequently-Asked-Questions#why-are-my-cookies-being-deleted).*

## Cookie 属性


Cookie属性默认值可以通过设置`Cookies.defaults`对象的属性来全局设置，也可以通过在最后一个参数中传递普通对象来单独地每次调用`Cookies.set（...）`。 每次调用属性会覆盖默认属性。

### expires

定义cookie何时被删除。值可以是 [`Number`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)，它将被解释为创建时的天数，或是 [`Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) 实例. 如果省略expires参数，该cookie将成为会话cookie。

要创建一个在不到一天的时间内到期的cookie，你可以查看 [FAQ on the Wiki](https://github.com/js-cookie/js-cookie/wiki/Frequently-Asked-Questions#expire-cookies-in-less-than-a-day).

**默认:** Cookie 将在浏览器关闭时被删除。

**示例:**

```javascript
Cookies.set('name', 'value', { expires: 365 });
Cookies.get('name'); // => 'value'
Cookies.remove('name');
```

### path

A [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) 表明cookie的可见路径。


**默认:** `/`

**示例:**

```javascript
Cookies.set('name', 'value', { path: '' });
Cookies.get('name'); // => 'value'
Cookies.remove('name', { path: '' });
```

**关于Internet Explorer的注意事项：**

> 由于底层WinINET InternetGetCookie的实现中存在一个模糊的错误，如果path属性包含文件名，IE的document.cookie将不会返回cookie。

(来自 [Internet Explorer Cookie Internals (FAQ)](http://blogs.msdn.com/b/ieinternals/archive/2009/08/20/wininet-ie-cookie-internals-faq.aspx))

这意味着无法使用`window.location.pathname`设置路径，以防这样的路径名包含如下文件名：`/check.html`（这样的cookie无法正确读取）。

实际上，你永远不应允许不受信任的输入来设置cookie属性，否则您可能会受到[XSS 攻击](https://github.com/js-cookie/js-cookie/issues/396).

### domain

A [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) indicating a valid domain where the cookie should be visible. The cookie will also be visible to all subdomains.

**Default:** Cookie is visible only to the domain or subdomain of the page where the cookie was created, except for Internet Explorer (see below).

**Examples:**

Assuming a cookie that is being created on `site.com`:

```javascript
Cookies.set('name', 'value', { domain: 'subdomain.site.com' });
Cookies.get('name'); // => undefined (need to read at 'subdomain.site.com')
```

**Note regarding Internet Explorer default behavior:**

> Q3: If I don’t specify a DOMAIN attribute (for) a cookie, IE sends it to all nested subdomains anyway?  
> A: Yes, a cookie set on example.com will be sent to sub2.sub1.example.com.  
> Internet Explorer differs from other browsers in this regard.

(From [Internet Explorer Cookie Internals (FAQ)](http://blogs.msdn.com/b/ieinternals/archive/2009/08/20/wininet-ie-cookie-internals-faq.aspx))

This means that if you omit the `domain` attribute, it will be visible for a subdomain in IE.

### secure

Either `true` or `false`, indicating if the cookie transmission requires a secure protocol (https).

**Default:** No secure protocol requirement.

**Examples:**

```javascript
Cookies.set('name', 'value', { secure: true });
Cookies.get('name'); // => 'value'
Cookies.remove('name');
```

## Converters

### Read

Create a new instance of the api that overrides the default decoding implementation.  
All get methods that rely in a proper decoding to work, such as `Cookies.get()` and `Cookies.get('name')`, will run the converter first for each cookie.  
The returning String will be used as the cookie value.

Example from reading one of the cookies that can only be decoded using the `escape` function:

```javascript
document.cookie = 'escaped=%u5317';
document.cookie = 'default=%E5%8C%97';
var cookies = Cookies.withConverter(function (value, name) {
    if ( name === 'escaped' ) {
        return unescape(value);
    }
});
cookies.get('escaped'); // 北
cookies.get('default'); // 北
cookies.get(); // { escaped: '北', default: '北' }
```

### Write

Create a new instance of the api that overrides the default encoding implementation:

```javascript
Cookies.withConverter({
    read: function (value, name) {
        // Read converter
    },
    write: function (value, name) {
        // Write converter
    }
});
```

## Server-side integration

Check out the [Servers Docs](SERVER_SIDE.md)

## Contributing

Check out the [Contributing Guidelines](CONTRIBUTING.md)

## Security

For vulnerability reports, send an e-mail to `jscookieproject at gmail dot com`

## Manual release steps

* Increment the "version" attribute of `package.json`
* Increment the version number in the `src/js.cookie.js` file
* If `major` bump, update jsDelivr CDN major version link on README
* Commit with the message "Release version x.x.x"
* Create version tag in git
* Create a github release and upload the minified file
* Change the `latest` tag pointer to the latest commit
  * `git tag -f latest`
  * `git push <remote> :refs/tags/latest`
  * `git push origin master --tags`
* Release on npm

## Authors

* [Klaus Hartl](https://github.com/carhartl)
* [Fagner Brack](https://github.com/FagnerMartinsBrack)
* And awesome [contributors](https://github.com/js-cookie/js-cookie/graphs/contributors)
