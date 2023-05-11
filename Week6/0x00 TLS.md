# 0x00 TLS


![[img/tls-layer.png]]

Secure Sockets Layer (SSL) 重命名为  Transport Layer Security (TLS)

X.509 证书标准
- 包含主题、主题的公钥、发行者名称等
- 发行人签署所有数据的散列
- 为了检查证书，我对所有数据进行哈希处理并检查颁发者的公钥。
- 如果我有发行者的公钥并且信任发行者，那么我就可以确定主体的公钥。

Protocol
1. $C\to S: N_C$
2. $S\to C: N_S, \text{Cert}_S$
3. $C\to S: E_S(K_{seed}), \{\text{Hash}_1\}_{K_{CS}}$
4. $S\to C: \{\text{Hash}_2\}_{K_{CS}}$
$\text{Hash}_1=\text{Hash}(N_C, N_S, E_S(K_{seed}))$
$\text{Hash}_1=\text{Hash}(N_C, N_S, E_S(K_{seed}), \{{\text{Hash}_1}\}_{K_{CS}})$
$K_{CS}$ 是基于 $N_C$, $N_S$, $K_{seed}$ 的session key

Protocol 基于 DHE（Diffie-Hellman） TLS-DHE
1. $C\to S: N_C$
2. $S\to C: N_S, g^x, \text{Cert}_S, \text{Sign}(\#(N_C, N_S, g^x))$
3. $C\to S: g^y, \{\#(\text{All Previous Msg})\}_{K_{CS}}$
4. $S\to C: \{\#(\text{All Previous Msg})\}_{K_{CS}}$
$K_{CS}$ 是基于 $N_C$, $N_S$, $g^{xy}$ 的session key

## Cipher Suites

![[img/cs.png]]


## 弱点

- 配置弱点 Configuration weaknesses：
	- 加密降级 Cipher Downgrade
	- 自签名证书
- 对实现的直接攻击：
	- Apple 的 goto fail bug
	- LogJam 攻击
	- 心脏出血 HeartBleed