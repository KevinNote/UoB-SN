# 0x01 Access Control


需要确保只有授权用户才能访问他们需要的内容
将讨论实现这一目标的方法和可能的陷阱（pitfalls）

## 访问控制矩阵 Access Control Matrix

![[img/ac.png]]

ACM 是所有主体和对象的矩阵
矩阵条目描述权限
问题：维护这样的矩阵可能很困难
如果矩阵损坏，则失去所有控制

## 访问控制列表 Access Control Lists (ACLs)

我们不想存储一个庞大的矩阵。
相反，我们可以将矩阵的每一列存储为
它所指的对象，例如。
(Accounts Data，\[(Sam, `r`), (Bob，`r`), (Accounts program, `rw`)])

### Unix ACL

![[img/unix-acl.png]]

![[img/unix-permission.png]]
![[img/dir.png]]
![[img/file.png]]
“s”权限表示程序在其所有者的权限下运行。

- 具有不同的User Indentifier（uid）：
	- 进程的真实 uid (ruid, real uid) 所有者
	- 有效 uid (euid, effective uid)：用于访问检查（文件系统除外）
	- 文件系统 uid (fsuid, filesystem uid)：用于文件的访问检查和所有权（通常等于有 euid）
	- saved user uid (suid)：当euid改变时，旧的euid保存为suid。 非特权进程只能将 euid 更改为 ruid 或 suid。

提供临时授予更高权限的灵活性，例如守护进程：以 root 身份启动（绑定到小于 1024 的端口）,然后将 ruid、euid 和 suid 设置为非特权值。 之后无法获得root权限

以特权用户身份运行的进程可能会将 euid 设置为非特权值，然后执行非特权操作，然后获得 root 权限

## 密码

Linux存储密码使用 (Salt, Hash)，因此即便是相同密码也会导致不同 Hash

### Windows

存储在 `system32/config/SAM`
需要管理员权限
基于其他key进行锁定并加密（并没有多少卵用）

### Windows Domain

密码Hash用于验证域中主机上的用户

缓存密码哈希以避免询问密码，这提升了毁灭攻击devastating attack（Pass-the-Hash）的可能性
1. 获取域中一台主机的用户凭据（例如网络钓鱼）
2. 利用漏洞成为本地管理员
3. 安装一个进程等待域管理员登录本机
3. 提取为域管理员缓存的哈希
4. 以域管理员身份登录
存在防御机制但使用起来很痛苦
ssh 好多了：公钥在不受信任的机器上，私钥在受信任的机器上

### 获取 Windows SAM文件

启动linux-获取SAM


### 常用的密码破解器

John the Ripper
- 最常用的暴力破解器
- 开源
Hashcat
- Claims to be the fastest/best
Ophacrack
- 最先进的免费彩虹表软件

### 常用攻击

1. 钓鱼 Phishing
	1. 防御：MFA
2. Password Injection
	1. 如果拥有硬碟的权限，或已有账户，想替换哈希到你知道的，换句话说：引导到第二个系统，直接操作
	2. 防御：给BIOS上锁。
	3. 应对此攻击：可以拔走硬盘，或重制BIOS

## 最佳安全

- 重要文件加密。
- 全盘加密
	- 加密整个硬盘
	- 密钥可以被暴力破解
	- 如果计算机处于睡眠模式则不安全。