# JWT (JSON Web Token)

JSON Web Token 是一种开放标准 ( [RFC 7519](https://tools.ietf.org/html/rfc7519) )，它定义了一种紧凑且独立的方式，用于在各方之间以 JSON 对象的形式安全地传输信息。该信息可以被验证和信任，因为它是经过数字签名的。JWT 可以使用密钥（使用 **HMAC** 算法）或使用 **RSA** 或 **ECDSA** 的公钥/私钥对进行签名。

## 使用场景

JSON Web Token 常用于以下场景的使用：

- **授权**：这是使用 JWT 最常见的场景。用户登录后，每个后续请求都将包含 JWT，从而允许用户访问该令牌允许的路由、服务和资源。单点登录是当今广泛使用 JWT 的一项功能，因为它的开销很小并且能够轻松地跨不同域使用。
- **信息交换**：JSON Web 令牌是在各方之间安全传输信息的好方法。因为 JWT 可以进行签名（例如，使用公钥/私钥对），所以您可以确定发送者就是他们所说的人。此外，由于签名是使用标头和有效负载计算的，因此您还可以验证内容是否未被篡改。

## 组成结构

JSON Web Teken 由被 `.` 分割的三个部分组成，三个组成部分分别是：

* Header （标头）
* Payload （载荷）
* Signature （签名）

因此，通常的 JWT 形如 `xxxxx.yyyyy.zzzzz` 。

### Header

标头 *通常* 由两部分组成：令牌的类型（JWT）和所使用的签名算法（例如 HMAC SHA256 或 RSA）。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

然后，对该 JSON 进行 **Base64Url** 编码以形成 JWT 的第一部分。

### Payload

令牌的第二部分是有效负载，其中包含 **声明（claims）** 。声明是关于实体（通常是用户）和附加数据的声明。声明分为三种类型： *注册* 声明、 *公开* 声明和 *私有* 声明。

* **Registered claims (注册声明)** ：这些是一组预定义的声明，不是强制性的，而是推荐的，以提供一组有用的、可互操作的声明。其中一些是： **iss** （issuer 发行者）、 **exp **（expiration time 到期时间）、 **sub **（subject 主题）、 **aud** （audience 受众）等。
* **Public claims (公开声明)** ：这些可以由使用 JWT 的人随意定义。但为了避免冲突，它们应该在 [IANA JSON Web 令牌注册表](https://www.iana.org/assignments/jwt/jwt.xhtml) 中定义，或者定义为包含防冲突命名空间的 URI。
* **Private claims (私有声明)** ：这些是为在同意使用它们的各方之间共享信息而创建的自定义声明，既不是 *注册* 声明也不是 *公开* 声明。

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

然后对有效负载进行 **Base64Url** 编码以形成 JSON Web 令牌的第二部分。

### Signature

要创建签名部分，您必须获取编码的标头、有效负载、密钥、标头中指定的算法，然后对其进行签名。

例如，如果要使用HMAC SHA256算法，则将通过以下方式创建签名：

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

签名用于验证消息在传输过程中没有发生更改，并且在使用私钥签名的令牌的情况下，它还可以验证 JWT 的发送者是否是其所说的人。

### 将以上部分组合

输出是三个由点分隔的 Base64-URL 字符串，可以在 HTML 和 HTTP 环境中轻松传递，同时与基于 XML 的标准（例如 SAML）相比更加紧凑。

下面显示了一个 JWT，它具有先前的标头和有效负载编码，并且使用密钥进行签名。

![编码 JWT](https://images.orkva.com/images/2023/08/01/encoded-jwt3.png)

如果您想使用 JWT 并将这些概念付诸实践，您可以使用 [jwt.io 调试器](https://jwt.io/#debugger-io) 来解码、验证和生成 JWT。

## JWT 如何工作

在身份验证中，当用户使用其凭据成功登录时，将返回 JSON Web 令牌。由于令牌是凭证，因此必须非常小心地防止安全问题。一般来说，您保留令牌的时间不应超过所需时间。

[由于缺乏安全性，](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html#local-storage)您也不应该将敏感会话数据存储在浏览器存储中。

每当用户想要访问受保护的路由或资源时，用户代理应该发送 JWT，通常使用**承载模式** 在 **授权** 标头中发送。标头的内容应如下所示：

```
Authorization: Bearer <token>
```

在某些情况下，这可以是无状态授权机制。服务器的受保护路由将检查标头中是否存在有效的 JWT `Authorization` ，如果存在，则将允许用户访问受保护的资源。如果 JWT 包含必要的数据，则可能会减少查询数据库以执行某些操作的需要，尽管情况可能并非总是如此。