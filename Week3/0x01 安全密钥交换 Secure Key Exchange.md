# 0x01 安全密钥交换 Secure Key Exchange

##  问题

当 Alice 和 Bob 在一个非安全信道（insecure channel）进行通信时，需要交换 secret key。
![[mitm.png]]

## 多轮解决方案 MultiRound Solution

![[mrs.png]]

## Diffie Hellman Key Exchange/DHX/DHKE

寻找一个素数 $p$ 以及一个满足 $g < p$ ，使得 $\text{gcd}(g, p-1)=1$

![[dhx.png]]


过程：

#### 一些看不懂的数学

横幂运算：

$$
c=b^e \mod{m}
$$

> **表示方法**
>  $(a^p-b)$ 是 $p$ 的倍数可以表示为 $a^p\equiv b \quad (\text{mod }{ p})$
>  
> 费马小定理：
> 表示：$a\in \mathbb{Z}, p \in \mathbb{P}$ 则 $a^p\equiv a \quad(\text{mod }p)$
> 或者：$a\in\mathbb{Z},p\in\mathbb{P}$ 则 $a^{p-1}\equiv 1 \quad(\text{mod }p)$

**TODO:**

#### Man-in-the-Middle Attack/MITM

![[dhx-mitm.png]]

DHX 也同样有安全问题，其保证了数据在传输过程中的安全性（无法被第三方查看），但是攻击者可以模拟一个接受人和发送人实现**中间人攻击**。

解决方案：
- **验证公钥 Authenticating Public Key**
- 需要：**受信的第三方：证书机构 Certification Authority (CA)**
