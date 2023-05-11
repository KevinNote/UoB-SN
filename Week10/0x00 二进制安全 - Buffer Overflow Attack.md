# 0x00 二进制安全 - Buffer Overflow Attack




缓冲区溢出攻击的简化高级视图
- x86 架构
- 堆栈溢出
- 专注于 32 位模式，但大多数内容也直接适用于 64 位模式

在 C 语言中，程序员需要告诉编译器如何管理内存，这很困难，如果出错，可能会允许攻击者执行任意代码（arbitrary code）

![[img/x86.png]]

## 寄存器
- EAX：累加器
- EIP：目前执行的汇编执行点
- ESP：栈顶
- EBP：栈底

![[img/reg1.png]]

## PUSH

![[img/stack.png]]

![[img/push.png]]

![[img/pushed.png]]

![[img/push2.png]]


![[img/pop.png]]

![[img/poped.png]]

![[img/pop-main.png]]

## Func' Call

```c
void main() {
	foo(1, 2);
}
```

```assembly
PUSH <2>
PUSH <1>
CALL <foo>
```

CALL 指令将函数的地址放入 EIP 并将旧的 EIP 存储在堆栈中。


ESP: 指向栈顶。 当您将数据压入或弹出到堆栈时，它会进入此处，然后调整 ESP 以反映新的堆栈头。

EBP: 指向栈帧（stack frame）的基址（base）。 当你调用一个函数时，它被保存在堆栈中，EBP 现在指向新堆栈帧的基址。 所有的局部变量都可以相对于这个指针找到。

## 栈帧保护

![[img/Pasted image 20230511194514.png]]

![[img/Pasted image 20230511194533.png]]

![[img/Pasted image 20230511194546.png]]

![[img/Pasted image 20230511194600.png]]

![[img/Pasted image 20230511194615.png]]

![[img/Pasted image 20230511194635.png]]

![[img/Pasted image 20230511194657.png]]

![[img/Pasted image 20230511194714.png]]

![[img/Pasted image 20230511194731.png]]

![[img/Pasted image 20230511194745.png]]

## Buffer Overflow Attack

- instruction pointer 控制程序执行
- instruction pointer 存储在 stack 上
- 我可以写入 stack

```c
// ..,
getStr();
// ...

void getStr() {
    char[16] buf;
    gets(buf);
}
```

```rust
[--buf[16 bytes]--][Old EBP][Old EIP][Stack] // char[16] buf
["Hello World\0"4B][Old EBP][Old EIP][Stack] // if entered "Hello World"

// 如果输入过长的数据可能会导致数据越界到 老EBP 和 老EIP
["Hello World XXX"][XXXX][XXXX][Stack] // if Entered > 16
                                       // EIP 损坏(corrupted)，segmentation fault

// 如果出现恶意（malice）代码
// 假设有函数位于 0x0097F9
["Hello World XXX"][XXXX][97F9][Stack] // if Entered > 16
// buf.           ][OEBP][OEIP][Stack]
```

## 防御

### The NX-bit

![[img/Pasted image 20230511200411.png]]

代码应该在内存的文本区域。 不在堆栈上。

NX 位提供了文本和堆栈之间的硬件区别（distinction）。

启用后，如果 EIP 指向堆栈，程序将崩溃。

针对 NX 位的标准攻击是重用内存可执行部分的代码。
- 跳转到程序中的另一个函数
- 跳转到标准 C 库中的函数（return to libc）
- 将现有代码的小片段串在一起（面向返回的编程）。

### Return-to-libc
-  Libc 是C 标准库。
-  它通常与可执行文件（executables）打包在一起以提供运行时环境。
-  它包括许多有用的调用，如运行任何命令的 `system`。
-  它链接到可执行内存（executable memory），因此绕过 NX 位保护。


### 地址空间布局随机化 Address Space Layout Randomization, ASLR
- 每次程序运行时，ASLR 都会向堆栈和代码库添加一个随机偏移量。
- 程序中的跳转被更改为指向正确的行。
- 这个想法是现在攻击者很难猜测他们注入代码的地址或特定函数的地址。
- 在所有操作系统中默认开启。

#### NOP Slide

- 在x86 中，操作码汇编指令(op code) `0x90` 什么都不做。
- 如果堆栈是 2MB，我可以注入 999000 字节的 `0x90`，然后是我的 shell 代码
- 然后我猜测返回地址并希望它在 2MB NOP 中的某处。
- 如果是，程序会将 NOP 向下滑动到我的 shell code。
- 通常与其他猜测随机性的方法一起使用。

### Metasploit

- Metasploit 是一个用于测试和执行已知缓冲区溢出攻击的框架。 
- 如果应用程序中的漏洞是众所周知的，他们将为其打补丁，同时也为它开发一个Metasploit 模块。 
- 如果应用程序未打补丁，它可能会被 Metasploit 接管。 
- Metasploit 还包括一个可以注入的shell code库。
- 将它用于您不拥有的机器是非法的。 不要这样做。

## Conclusion

缓冲区溢出是 C 语言中内存管理不善（poor memory management）的结果：
- 即使是“最好的”程序员也会犯错误。
- 缓冲区溢出攻击利用这些来覆盖内存值。
- 这允许攻击执行任意代码（arbitrary code）。