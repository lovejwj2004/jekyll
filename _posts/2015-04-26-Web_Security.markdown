---
layout:     post
title:      "Web Security"
subtitle:   "Web"
date:       2015-04-26 22:53:00
author:     "chne"
header-img: "img/post-security-bg.jpg"
---

最近因为工作的原因进行了一次Web安全培训，说实话对于这一块的知识以前基本上没有什么概念和经验。而且，因为工作后的项目基本上都是企业内部的引用或者是公司自己内部的系统在安全这块因为基本上是在内网使用所以安全这块也没有像B2C这种电商网店特别注意刚好今天晚上看到了和这个注意相关的系列文章。

> Web Security

<h2 class="section-heading">加密机制</h2>

<h3>加密算法</h3>

数据加密算法  

+   对称加密
+   非对称加密
+   信息摘要

对称加密: 使用单个密钥对数据进行加密和解密。

非对称加密: 使用一对密钥(公钥和私钥)对数据进行加密和解密。

+  使用公钥加密数据，使用私钥解密数据。
+  使用私钥签名数据，使用公钥验证签名。

信息摘要: 加密过程不需要密钥，并且经过加密的数据无法被解密，它是根据不定长的明文计算得到一段定长的数据。(MD5、SHA1)

密钥存储:

+  DER：二进制编码。
+  PEM：ASCII编码。

<h2 class="section-heading">安全概述</h2>

信息安全的核心问题是要保障数据的合法使用者能够在任何需要该数据时获得保密的，没有被非法更改过的数据

+ 机密性
+ 完整性
+ 可用性
+ 可审计性
+ 不可抵赖性

风险评估
<a href="#">
    <img src="{{ site.baseurl }}/img/post-security-01.png" alt="Risk Calculate">
</a>

<h2 class="section-heading">浏览器的跨域访问</h2>


<h3>浏览器内核</h3>

+ Trident:IE使用的内核。
+ Gecko:Firefox使用的内核。
+ WebKit:Safari和Chrome使用的内核。
+ Presto:Opera使用的内核。


<h3>同源策略</h3>
它认为任何站点的内容都是不安全的，所以当脚本运行时，只被允许访问来自同一站点的资源。
同源是指域名、协议、端口都相同

<h3>跨域访问</h3>
1. iFrame
	1. 当主域名相同，子域名不同时只要在js中设置相同的document.domain.
	2. 当主域名不同时比较麻烦，具体的方法可以见参考.
2. Ajax
	1. JSONP, 即script标签的src下加载的js不受同源策略限制，且加载后的js运行在当前页面的域下所以可自由操作当前页面的内容。
	2. 在b.com的服务器返回信息中增加以下头信息：
		- Access-Control-Allow-Methods:GET
		- Access-Control-Allow-Origin:http://a.com
	3. 使用HTML5的postMessage方法实现跨域访问。


<h2 class="section-heading">常见的攻击和防御</h2>

<h3>XSS</h3>
1. 反射型XSS,诱使用户打开一个恶意链接服务端将链接中参数的恶意代码渲染到页面中，再传递给用户由浏览器执行，从而达到攻击的目的。
<pre><code>http://a.com/a.jsp?name=xss<script>alert(1)</script>
</code></pre>
2.持久型XSS,将恶意代码提交给服务器，存储在服务器端当用户访问相关内容时再渲染到页面那种，以达到攻击的目的。
<h3>Cookie劫持</h3>
通过诱使用户打开一个链接，调用其它域名下的js获取当前页面的cookie对象。  

Solution:防御Cookie劫持的一个简单的方法是在Set-Cookie时加上HttpOnly标识，浏览器禁止JavaScript访问带HttpOnly的Cookie。

XSS防御: 

1. 输入检查,对特殊字符做编码或过滤。 一定要在后台做检查。

2. 输出检查,对渲染到HTML中内容执行HtmlEncode,对渲染到JS中的内容执行JavascriptEncode。


<h2 class="section-heading">SQL注入</h2>
主要原因是因为用户提交的数据被用来当成命令来执行，比如下面的代码:
<pre><code>select * from user where username = 'leo'; drop table user--'
</code></pre>

Solution: 
使用预编译语句
<pre><code>String sql = "select * from user where username = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, username);
ResultSet results = pstmt.executeQuery();
</code></pre>

<h2 class="section-heading">跨站请求伪造(CSRF)</h2>
由于操作所需的所有参数都能被攻击者得到，进而构造出一个伪造的请求，在用户不知情的情况下被执行。比如，某项操作是需要用户在登陆后才能进行可以通过伪造的链接来实现。

Solution:

- 验证码，确保用户在进行关键操作的时候知晓。
- referer检查
- Anti CSRF Token,生成一个随机的token通过用户提交的时候在服务器端对比。

<h2 class="section-heading">点击劫持(ClickJacking)</h2>
点击劫持是从视觉上欺骗用户。攻击者使用一个透明的iframe覆盖在一个网页上，诱使用户在该网页上操作，而实际点击却是点在透明的iframe页面。

Solution:

针对iframe的攻击，可使用一个HTTP头：X-Frame-Options，它有三种可选值：

- DENY： 禁止任何页面的frame加载；
- SAMEORIGIN：只有同源页面的frame可加载；
- ALLOW-FROM：可定义允许frame加载的页面地址。


针对图片覆盖攻击，则注意使用预防XSS的方法，防止HTML和JS注入。

<h2 class="section-heading">References</h2>
- http://blog.gopersist.com/2015/04/08/crypto/
- http://blog.gopersist.com/2015/04/17/web-security-2/
- http://blog.gopersist.com/2015/04/22/web-security-3/
- http://blog.gopersist.com/2015/04/25/web-security-4/
