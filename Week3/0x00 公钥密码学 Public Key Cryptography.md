# 0x00 公钥密码学 Public Key Cryptography

## Directions
- 保密性/Confidentiality
- 信息完整性/Message Integrity
- 发件人认证/Sender Authentication
- （SOFT）发送方不可否认性(不可抵赖性) /Sender Undeniability

## Kerckhoffs’ Principle

一个加密系统应该是安全的，即使关于该系统的一切都是公开的，除了密钥。
A cryptographic system should be secure even if everything about the system, except the key, is public knowledge.

现代应用甚至需要***防篡改（Tamper-Resistance）***

## Semmetric Key Cryptography

```
[ALICE] originalDoc -{SecretKey}-> encDoc
        encDoc      -{SecretKey}-> originalDoc [BOB]
```
**核心问题：如何去分享密钥**

### 主要的瓶颈（Bottleneck）

每一对（pair）人需要一个独立密钥。如下图，5个人需要最少4个密钥：

```
A     B
 \   /
   C
 /   \
D     E
```

因此每个人最少需要 $(n-1)$ 个不同的密钥
因此需要，$\frac{(n-1)n}{2}$ 个密钥，是非常不适合做**密钥管理的 （Key Management）** 。

## Public Key Cryptography

用户拥有 Pub Key 和 Secret Key/Priv Key。
公钥是面向大众的，而私钥自我保管。

### Encryption

```
[BOB] originalText -{ALICE's PubKey}--> cipherText
      cipherText   -{ALICE's PrivKey}-> originalText [ALICE]
```
对于 $n$ 个人，我们不再需要 pairwise distinct 个密钥，而需要 $n$ 对密钥。

### Authentication

```
[BOB] doc       -{BOB's PrivKey (SIGN)}--> signedDoc
      signedDoc -{BOB's PubKey (VERIFY)}-> Accept/Reject [ALICE]
```

### Public Key Infrastructure

![[pk-infrastructure.png]]
![[infra-2.png]]