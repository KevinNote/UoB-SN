# 0x02 RSA

## 算法

包含3个算法，Gen，Enc 和 Dec

### Gen

输入安全参数（security parameter）$\lambda$
- 生成拥有同样 $\lambda$ bits 的两个不同素数，$p, q$
- 计算 $N=pq$, $\phi(N)=(p-1)(q-1)$
- 选择一个随机整数 $e\in (1, \phi{(N)})$ 满足 $\text{gcd}(e, \phi{(N)})=1$
- 定义 $\mathbb{Z}^+_N=\{x \mid x\in(0, N)\quad \text{and}\quad \text{gcd}(x,N)=1\}$
- 计算 $d$ 满足 $ed\equiv 1 \quad({\text{mod }(\phi(N))})$
- 公钥 $PK=(e, N)$, 私钥 $SK=e, d, N$

### Enc
$\text{INPUT: } PK, m\in\mathbb{Z}^+_N$
$\text{OUTPUT: } c = m^e (\text{mod }(N))$

### Dec
$\text{INPUT: } SK, c\in\mathbb{Z}^+_N$
$\text{OUTPUT: } m = c^d (\text{mod }(N))$

### Example

1. INPUT: $p = 3$, $q = 11$
2. $N=pq=33$, $\phi(N)=(p-1)(q-1)=2\times 10=20$
4. Let $e=7$
	- Note: $\text{gcd}(e,\phi(N))=\text{gcd}(7, 20)=1$
5. $d=3$, ➡️  $e\times d=7\times 3 = 21=20+1=\phi{(N)}+1$
6. $PK=(e, N)=(7, 33)$
7. $SK=(e, d, N)=(7, 3, 33)$

$\mathbb{Z}^+_N=\{1,2,4,5,7,8,10,13,14,16,17,19,20,23,25,26,28,29,31,32\}$
Enc
1. $m=4$
2. $c=m^e (\text{mod }(N))=4^7 \mod{33}=16$
Dec
1. $c=16$
2. $m= c^d (\text{mod }(N))=16^3\mod{33}=4$
