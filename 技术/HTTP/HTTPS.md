# HTTPS

## HTTP vs HTTPS

<img src="./assets/http-vs-https.png">

## 对称加密

<img src="./assets/对称加密.png">

## 非对称加密

<img src="./assets/非对称加密.png">

浏览器和服务器在使用 TLS 建立连接时需要选择一组恰当的算法来实现安全通信，这些算法的组合被称为 “密码套件”。

TLS 的密码套件命名非常规范：密钥交换算法 + 签名算法 + 对称加密算法 + 摘要算法。
如 `ECDHE-RSA-AES256-GCM-SHA384` 表示：

- 握手时使用 `ECDHE` 算法进行密钥交换
- 用 `RSA` 签名和身份认证
- 握手后的通信使用 `AES` 对称算法，密钥长度 256 位
- 分组模式是 `GCM`
- 摘要算法 `SHA384` 用于消息认证和产生随机数
