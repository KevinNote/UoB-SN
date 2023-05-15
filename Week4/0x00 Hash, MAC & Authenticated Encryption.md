# 0x00 Hash, MAC & Authenticated Encryption

## 动机

虽然我们可以保证通信加密，但是我们仍然需要去检测篡改（manipulation） CipherText。

## Hash

- 一条消息的哈希总是相同的
- 任何微小的变化都会使 Hash 完全不同
- 从 Hash 得到消息是非常困难的
- 任何两个不同的消息都不太可能具有相同的 Hash

- 验证下载的消息
	- 将消息的各个部分捆绑在一起（Hash整个消息）
	- 对消息进行 Hash 处理，然后对哈希进行签名（用于电子签名）
- 保护密码
	- 存储哈希值，而不是密码

### 攻击

- 原像攻击 Preimage attack：查找给定 Hash 的消息：非常困难
- 碰撞攻击 Collision attack：找到两条具有相同 hash 的消息
- 前缀冲突攻击 Prefix collision attack：攻击者可以为消息选择前缀的冲突攻击

#### 生日悖论 Birthday Paradox

- 有多少个人时，出现相同生日的概率超过 0.5？
- 23 个人，有23对生日
- 2个人不同生日的概率为 $\frac{364}{365}$
- 23 个人为$\frac{364}{365}^{23}=0.4995$

### SHA

#### SHA1
- 160 bit
- 进行一次 Birthday Paradox 需要进行 $2^{80}$次测试
- 2005，$2^{63}$ 次的攻击被找到
- 无人信任

#### SHA2

- improved & longer SHA1
- 256 bit, 512 bit
- 基于 SHA1 因此具有相同的安全问题

#### SHA3
- Keccak

### Merkle–Damg˚ard (MD) Hashes

- MD4/MD5
	- 仅当我们只关心原像攻击或完整性时才有用。
- MD6
	- SHA3 的 Candidate

## MAC/Message Authentication Codes

```php
$msg, MAC($k, $msg) // $k 是共享的密钥
```

可能对 MAC 的攻击：在不知道密钥的情况下向 MAC 添加数据（长度扩展攻击）

### Block Cipher Mode

CBC
![[Week4/img/cbc.png]]

// TODO:

### Hash to MAC

我们可以尝试使用如下进行

$$
\text{MAC}_{key}(M) = \text{Hash}(M, key)
$$
但这可能会导致长度扩展攻击。
![[img/hash.png]]



## Authenticated Encryption

### 密文可能被替换Altered

使用特定密钥的 AES 加密将任何 128 位块映射到 128 位块（或 256）
AES 解密还将任何 128 位块映射到 128 位块。 解密可以在任何块上运行（不仅仅是加密）。

CBC 模式：任何更 改都会影响消息的所有其余部分。
ECB 模式：任何变化只影响区块。
CTR 模式：任何更改仅影响更改的位。
CTR 模式：如果我知道明文，我可以更改加密的消息。

使用 AEM，仅当您知道密才能形成有效的密文。
最常见的方法是在密文中添加 MAC。

### CCM

1. 首先先计算数据的 AES CBC-MAC。
2. 然后使用相同的密钥和 CTR 模式加密 后跟 MAC 的消息

## Conclusion

Hash：一般检测消息的损坏。 攻击者可以生成新的哈希值
MAC (Message Authentication Codes)：使用密钥来确保消息未被更改
AEM：提供加密，以便可以检测到对密文的操纵。 经常使用 MAC。