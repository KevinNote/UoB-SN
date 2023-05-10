# 0x01 Protocol

$C(A)$ 表示 C 扮演 A

$\{\text{\_}\}_{K_{ab}}$ 表示对称加密

$E_X\{\_\}$ 表示使用 $X$ 的公钥加密

$S_X(\_)$ 使用 $X$ 的私钥进行签名


## 明文发送

Send: A to B
$A\to B: \text{"Hi"}$

### **普通窃取**
$A\to B: \text{"Hi"}$
$E(A)\to B: \text{"Hi"}$
攻击者可以假装pretend 自己是任何人

## **Symmetric Key Enc:**
$A\to B:\{\text{"Im Alice"}\}_{K_{ab}}$



### **Replay Attack/重放攻击:**
$A\to B:\{\text{"Im Alice"}\}_{K_{ab}}$
$E(A)\to B:\{\text{"Im Alice"}\}_{K_{ab}}$
攻击者可以 intercept 并重新发送（重放）这条信息


## 随机数 Nonce

仅使用一次的数字（通常用于质询/响应设置）

1. $A\to B: \text{A}$
2. $B\to A: \{N_a\}_{K_{ab}}$
3. $A\to B: \{N_a+1\}_{K_{ab}}, \{\text{Pay Elvis \$5}\}_{K_{ab}}$

### 问题：重放密文

1. $A\to B: \text{A}$
2. $B\to A: \{N_a\}_{K_{ab}}$
3. $A\to B: \{N_a+1\}_{K_{ab}}, \{\text{Pay Elvis \$5}\}_{K_{ab}}$

可能的攻击

1. $A\to B: \text{A}$
2. $B\to A: \{N_a\}_{K_{ab}}$
3. $A\to B: \{N_a+1\}_{K_{ab}}, \{\text{Pay Elvis \$5}\}_{K_{ab}}$
4. $A\to B: \text{A}$
5. $B\to A: \{N_{a2}\}_{K_{ab}}$
6. $A\to E(A): \{N_{a2}+1\}_{K_{ab}}, \{\text{Pay Bob \$5}\}_{K_{ab}}$
7. $E(A)\to B: \{N_{a2}+1\}_{K_{ab}}, \{\text{Pay Elvis \$5}\}_{K_{ab}}$

攻击者在第三步知道了 $\{\text{Pay Elvis \$5}\}_{K_{ab}}$
在6步时候，A本意是发送给B，但是却被 E(A) 拦截，从而被 E(A) 获得了 $\{N_{a2}+1\}_{K_{ab}}$
然后 E(A) 伪造自己为 A 并发送给 B $\{N_{a2}+1\}_{K_{ab}}, \{\text{Pay Elvis \$5}\}_{K_{ab}}$

去修正这个错误，应该更改传输协议为
1. $A\to B: \text{A}$
2. $B\to A: \{N_a\}_{K_{ab}}$
3. $A\to B: \{N_{a}+1, \text{Pay Elvis \$5}\}_{K_{ab}}$

通过上述协议可以做到：
1. B清楚自己在和 A 通讯
2. A想给Elvis发送 $5
3. A的消息是最新的，不会被重放

Key Establishment Protocol
• This protocol was possible because A and B  shared a key.
• Often, the principals need to set up a session key using a Key Establishment Protocol.
• To be sure they are communicating with the correct principal, they must either know each others public keys or use a Trusted Third Party (TTP).

## 公钥加密

### Needham-Schroeder Public Key Protocol/NH PKP

1. $A\to B: E_B\{N_a, \text{A}\}$
2. $B\to A: E_A\{N_a, N_b\}$
3. $A\to B: E_B\{N_b\}$

Na Nb 可以用来生成对称加密
**目标**：Alice 和 Bob 确定他们正在互相交谈，并且只有他们知道密钥

#### MITM

攻击者 C
1. $A\to C: E_C\{N_a, \text{A}\}$
	1. $C(A)\to B: E_B\{N_a, \text{A}\}$
	2. $B\to C(A): E_A\{N_a, N_b\}$
2. $C\to A: E_A\{N_a, N_b\}$
3. $A\to C: E_C\{N_b\}$
	1. $C(A)\to B: E_B\{N_b\}$

### 修正

原始：
1. $A\to B: E_B\{N_a, \text{A}\}$
2. $B\to A: E_A\{N_a, N_b\}$
3. $A\to B: E_B\{N_b\}$

修正为：

1. $A\to B: E_B\{N_a, \text{A}\}$
2. $B\to A: E_A\{N_a, N_b, \text{B}\}$
3. $A\to B: E_B\{N_b\}$

## 前向安全 Forward Secrecy

> Perfect Forward Secrecy: 用来产生会话密钥(session key)的长期密钥(long-term key)泄露出去，不会造成之前通讯时使用的会话密钥(session key)的泄露，也就不会暴漏以前的通讯内容。简单的说，当你丢了这个long-term key之后，你以后的行为的安全性无法保证，但是你之前的行为是保证安全的
>
> Forwar Secrecy: 敌手获取到了你当前的密钥，但是也无法成功伪造一个过去的签名。
>
> 简单的说，这两个概念是用在不同的环境中，但是其意图是一样的：保证密钥丢失之前的消息安全性或签名的不可伪造性。
> 
> Source: [如何理解前向安全性？和完美前向保密（perfect forward secrecy）区别？——知乎](https://www.zhihu.com/question/45203206/answer/98980606)

> GPT3.5:
> 
> Forward Secrecy (前向保密)是指，在通信过程中使用的密钥即使被泄露或者被攻击者获取，也不能用来解密过去的通信记录。这样做可以保证通信的机密性不会因为密钥泄露而被破坏。
> 
> Perfect Forward Secrecy (完美前向保密)是一种更加严格的前向保密机制，它保证了即使长期秘钥被泄露，也不会破坏之前通信的保密性。在使用 Perfect Forward Secrecy 时，每次会话都使用不同的加密密钥，这些密钥会随机生成，并且只在该会话期间使用。这样做的好处是，在密钥泄露的情况下，攻击者也只能获取到单个会话的密钥，而不是整个通信记录的密钥，从而保证了所有历史通信的机密性。
> 
> 因此，Perfect Forward Secrecy 是一种更加安全的加密机制，可以防止长期秘钥的泄露和攻击。

1. $A\to B: E_B\{N_a, \text{A}\}$
2. $B\to A: E_A\{N_a, N_b, \text{B}\}$
3. $A\to B: E_B\{N_b\}$
4. $B\to A: \{M\}_{key(N_a, N_b)}$

// TODO: NH Protocol 是否具有 FS？

Secure against the “standard” attacker: intercept, replay, delete, alter

而政府可以合法地强制人们交出他们的私钥。（人是最不安全的）

如果协议对拥有以下信息的攻击者保密消息，则该协议具有前向保密性：
- 协议运行的记录
- 委托人的长期密钥

防止政府强制人们交出密钥，或防止黑客窃取密钥。


### Station-to-Station Protocol

1. $A\to B: g^x$
2. $B\to A: g^y, \{S_B(g^y, g^x)\}_{g^{xy}}$
3. $A\to B: \{S_A(g^y, g^x)\}_{g^{xy}}$
4. $B\to A: \{M\}_{g^{xy}}$

- $x$, $y$, $g^{xy}$ 在协议运行后不会存储
- A B的密钥阻止攻击者读取 M
- STS 拥有 FS

## 证书 Certificates

如果需要交互的双方不知道对方的公钥，如何进行通讯？
- f2f 或者：
- 通过可信第三方（Trusted Third Party）去签名他们的 identity 与 公钥

> 数字证书就是经过CA认证过的公钥，因此数字证书和公钥一样是公开的。
>
> 可以这样说，数字证书就是经过CA认证过的公钥，而私钥一般情况都是由证书持有者在自己本地生成或委托受信的第三方生成的，由证书持有者自己负责保管或委托受信的第三方保管。
> 
> Source: [公钥、私钥、数字证书的关系是什么？ - 华为云](https://support.huaweicloud.com/intl/zh-cn/ccm_faq/ccm_01_0122.html)


### Full Station-to-Station Protocol

1. $A\to B: g^x$
2. $B\to A: g^y, \{\text{Cert}_B, S_B(g^y, g^x)\}_{g^{xy}}$
3. $A\to B: \{\text{Cert}_A, S_A(g^y, g^x)\}_{g^{xy}}$
4. $B\to A: \{M\}_{g^{xy}}$


### The Needham-Schroeder Key Establishment Protocol

原始 NH协议
1. $A\to B: E_B\{N_a, \text{A}\}$
2. $B\to A: E_A\{N_a, N_b, \text{B}\}$
3. $A\to B: E_B\{N_b\}$
4. $B\to A: \{M\}_{key(N_a, N_b)}$

$A$, $B$ 使用 TTP $S$ 建立密钥 $K_{ab}$

1. $A\to S: \text{A}, \text{B}, N_a$
2. $S\to A: \{N_a, \text{B}, K_{ab}, \{K_{ab}, \text{A}\}_{K_{bc}}\}_{K_{as}}$
3. $A\to B: \{K_{ab}, \text{A}\}_{K_{bs}}$
4. $B\to A: \{N_b\}_{K_{ab}}$
5. $A\to B: \{N_b+1\}_{K_{ab}}$

对于 NH KEP，$A$ 可以重复使用很久以前的密钥

## 实体鉴权 Entity Authentication

### 密钥建立（Key Establishment）目标

- 密钥新鲜度（Freshness）：建立的密钥是新的（来自某些受信任的第三方或因为它使用了新的随机数）。
- 密钥排他性（Exclusivity）：密钥只有协议中的委托人知道。
- Good Key：钥匙既新鲜又排他

### 鉴权（Authentication）目标

**Far-end Operative:** A 知道 B 目前活跃

例如，对于实例 B 可以签名 A 生成的随机数
1. $A \to B: N_a$
2. $B\to A: S_B(N_a)$
但是不足以解决所有问题，例如NH KEP

**Once Authentication:** A知道B希望与A通讯。

例如，对于实例 B，可以发送包含 A 名字的签名：
$B\to A: S_B(\text{A})$

### Conclusion

实体鉴权 = 密钥建立 + 健全

A 只要 B 当前活跃并且知道 B 希望和 A 通信。
例如：
1. $A\to B: N_a$
2. $B\to A: S_B(\text{A}, N_a)$

![[img/gkea.png]]

### 最高目标：密钥共识 Mutual Belief

协议为 A 和 B 提供密钥的共识，如果 A 在运行协议后，B 可以确定：
- $K$ 是和 $A$ 的 Good Key
- $A$ 可以确认 $B$ 希望使用 $K$ 与 $A$ 通讯
- $A$ 知道 $B$ 相信 $K$ 是 Good Key

![[img/mb.png]]

简单来说
- 密钥共识 Mutual Belief
	- 良好的密钥 Good Key
		- Fresh Key: 密钥的实效性
		- Key Exclusive：密钥只有委托人知晓
	- 实体验证 Entity Authentication
		- FEO：知晓活跃状态
		- OA：知晓通讯请求发起人和目标


NH PKE
1. $A\to C: E_C\{N_a, \text{A}\}$
	1. $C(A)\to B: E_B\{N_a, \text{A}\}$
	2. $B\to C(A): E_A\{N_a, N_b\}$
2. $C\to A: E_A\{N_a, N_b\}$
3. $A\to C: E_C\{N_b\}$
	1. $C(A)\to B: E_B\{N_b\}$

1. FK：密钥实效性：是，因为使用随机数 Na
2. KE：密钥只有委托人知晓：否，C知晓了
3. FEO：活跃状态：是 // TODO: 什么情况不知晓？
4. OA：不知晓，无法确认是 B 在和 A 通讯