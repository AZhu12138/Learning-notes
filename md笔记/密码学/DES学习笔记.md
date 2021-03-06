# 1、DES概况

## 1.1、DES是什么

DES全称为Data Encryption Standard，即数据加密标准，是一种使用密钥加密的块算法。

## 1.2、DES整体特点

①分组密码：

- 明文、密文和密钥的分组长度都是64位。

②面向二进制的密码算法：

- 因而能够加解密任何形式的计算机数据。

③对合运算：

- **f = f-1**  
- **加密和解密共用同一算法，使工程实现的工作量减半。**

④综合运用了置换、代替、代数等基本密码技术。 

**⑤基本结构属于Feistel结构。** 

# 2、 DES算法结构

## 2.1、整体框图

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115162455.png)

## 2.2、加密过程

1 、64位密钥经**子密钥产生算法**产生出16个子密钥：K1，K2，...，K16，分别供第一次，第二 次，...，第十六次加密迭代使用。

**2 、64位明文经初始置换IP，将数据打乱重排并分成左右两半。左边为 L0 ，右边为 R0 。** 

3 、第一次加密迭代：

- **在子密钥 K1的控制下，由加密函数 f 对 R0 加密： L0 ⊕  f （ R 0 ， K1 )** 
- **以此作为第二次加密迭代的R1，以 R0 作为第二次加密迭代的 L1 。**  

4 、第二次加密迭代至第十六次加密迭代分别用子密钥 K2 ，... ， K16进行，其过程与第一次加密迭代相同。 

**5 、第十六次加密迭代结束后，产生一个64位的数据组。以其左边32位作为 R16 ，以其右边32位作为 L16 。** 

**6 、 R16 与 L16合并，再经过逆初始置换 IP–1 ，将数据重新排列，便得到 64位密文。** 

7 、DES加密过程的数学描述： 

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115162808.png)

# 3、子密钥的产生 

## 3.1、作用

- 64位密钥经过**置换选择1**、**循环左移**、**置换选择2** 等变换，产生16个子密钥 K1，K2，… K16 
- 分别供各次加密迭代使用 分别供各次加密迭代使用。 

## 3.2、整体框图

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115164238.png)

## 3.3、置换选择1 

### 3.3.1、作用

- 去掉密钥中的8个奇偶校验位。
- 打乱重排，形成 C0 (左28位)，D0 (右28位) 。 

### 3.3.2、矩阵

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115164527.png)

### 3.3.3、说明

- 矩阵中第一个数字47，表明原密钥中的第47位移到 C0 中的第一位。 

## 3.4、循环移位  

### 3.4.1、作用

- 对C0 ，D0 分别循环左移位。 

### 3.4.2、循环移位表 

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115164859.png)

## 3.5、置换选择2

### 3.5.1、作用

- **从Ci 和 Di (56位)中选择出一个48位的子密钥Ki**

### 3.5.2、矩阵 

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115165037.png)

### 3.5.3、说明

- 从Ci中取出24位，从Di 中取出24位，形成 48位的子密钥Ki 

# 4、初始置换 IP

## 4.1、作用

- **把64位明文打乱重排**
- 左一半为 L0 (左32位)，右一半为R0 (右32位) 。
  - 例如：把输入的第1位置换到第40位，把输入的第58位置 换到第1位。 

## 4.2、矩阵

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115165353.png)

# 5、逆初始置换 IP-1 

##  5.1、作用

- 把64位中间密文打乱重排。
- 形成最终的64位密文。 

## 5.3、相逆性

- IP与IP－1互逆。

  例：在IP中把输入的第1位置换到第40位，而在IP－1中 把输入的第40位置换到第1位。

## 5.3、保密作用不大 

- 由于没有密钥参与，在IP和IP-1公开的条件下，其保密意义不大 

## 5.4、矩阵

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115165709.png)

# 6、 加密函数 f 

## 6.1、作用

- **DES的轮函数，DES保密的核心。** 

## 6.2、整体框图

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115170048.png)

## 6.3、选择运算E

- 把32位输入扩充为48位中间数据；
- **通过重复使用数据，实现数据扩充。**
- 矩阵： 

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115170151.png)

## 6.3、代替函数组S（S盒）

### 6.3.1、S盒的一般性质：

- **S盒是DES中唯一的非线性变换，是DES安全的关键。** 

- 在保密性方面，起混淆作用。 

- 共有8个S盒，并行作用。 

- **每个S盒有6个输入，4个输出，是非线性压缩变换。** 

- 设输入为 b1 b2 b3 b4 b5 b6 ，则**以 b1 b6 组成的二进制数为行号，b2 b3 b4 b5 组成的二进制数为列号**。行列交点处的数 （二进制）为输出。 

- 举例：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115170604.png)

### 6.3.2、S盒的设计准则 

1976年，NSA公布的DES 的 S盒设计准则：

- P0：每个 S盒的每一行都是整数 0 到15的一个置换； 
- P1：每个 S盒的输出不是它的输入的线性或仿射函数； 
- P2：改变 S盒的任一输入比特，其输出至少有两比特发生 改变；
- P3：对任一 S盒和任一输入 x ，S(x) 和S(x ⊕001100)至少有 两位发生变化（这里 x是一个长度为 6的比特串）； 
- P4：对任何 S盒和任一输入 x，以及e,f ∈{0,1}, 有 S(x) ≠S(x ⊕11ef00)，其中 x是一个长度为 6的比特串；
- P5：对任何 S盒，当它的任一输入比特位保持不变，其它 5 位改变时，输出数字中 0 和 1的数目大致相等。 

其它准则：

美国NSA至今没有完全公布S盒的设计细节。研究表明，除了上述准则外，还有一些其它准则。

- 非线性度准则：S盒必须有足够的非线性度，否则不能抵 抗线性攻击；
- 差分均匀性准则： S盒的差分性应均匀，否则不能抵抗差 分攻击；
- 代数次数及项数分布准则 ：S盒必须有足够的代线次数和 项数，否则不能抵抗插值攻击和高阶差分攻击； 

## 6.4、置换运算P

- 把数据打乱重排。
- **在保密性方面，起扩散作用：**
  - 因为S盒是6位输入，4位输出，其非线性作用是局部的 
  - 因此，需要把S盒的混淆作用扩散开来

- **S盒与P置换的互相配合，共同确保DES的安全。** 
- 矩阵：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115171044.png) 

# 7、 解密过程 

- **DES的加密算法是对合运算，因此解密和加密可共用同一个算法。**

- 不同点：**子密钥使用的顺序不同**。

- 第一次解密迭代使用子密钥 K16 ，第二次解密迭代 使用子密钥 K15 ，第十六次解密迭代使用子密钥 K1 。

- DES解密过程的数学描述： 

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115171446.png)

# 8、DES的安全性问题

##  8.1、攻击 

- 穷举攻击。目前最有效的方法。
- 差分攻击。
- 线性攻击。 

## 8.2、安全弱点 

- 密钥太短。
- 存在弱密钥。
- 存在互补对称性。

# 9、 3重DES 

## 9.1、3DES的优势：

- 3密钥的3DES：密钥长度是168位。
- 2密钥的3DES：密钥长度是112位。
- **安全：密钥足够长； 经过最充分的分析和实践检验。**
- **兼容性好。** 

## 9.2、3DES的弱势：

- 速度慢。 

## 9.3、2密钥3DES框图 

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201115171847.png)





