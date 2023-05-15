# Cheat Sheet

## 词汇表

Eavesdropping 窃听
Block Ciphers 分组密码
Stream Cipher 流式密码
Confidentiality 保密性
Message Integrity 消息完整性
Tamper-Resistance. 防篡改
Man-in-the-Middle Attack
impersonate 扮演
Forward Secrecy 前向安全


## Hash Attck
-   _Preimage attack_: Find a message for a given hash: 原像攻击
-   _Collision attack_:Find two messages with the same hash.
-   _Prefix collision attack_: A collision attack where the attacker can pick a prefix for the message.
MAC/Message Authentication Codes
Authenticated Encryption
CCM


## Web 可能的攻击

1. Cookie 窃取
	1. Solution：使用 TLS
2. 认证有误
	1. 未使用 Session（Cookie无过期时间）
		1. Solution：设置过期
	2. 密码未Hash
3. Sensitive Data Exposure 敏感信息暴露
	1. 明文传输
	2. 明文储存
	3. 未Full TLS
4. SQL Injection
	1. Solution：清理输入：`mysqli_real_escape_string(_db, string)`
	2. Solution：prepared statement
		1. `$conn->prepare("INSERT INTO People (firstname, lastname) VALUES (?, ?)"); $stmt->bind_param("ss", $firstname, $lastname);`
5. Cross Site Script (XSS)
	1. Reflected XSS 反射型XSS
	2. Stored XSS 储存型XSS
	3. Solution: sanitisation `htmlspecialchars()`
6. Cross-site Request Forgery (CSRF)
	1. Solution: Referer Header
		1. 容易被伪造
	2. Solution: 添加 Token
	3. Solution: 使用 Nonce
7. XML External Entities (XXE)
8. Broken Access Control
9. Path Traversal