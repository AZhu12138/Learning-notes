### 1、实验目标

通过本实验，掌握使用VHDL硬件描述语言设计一个1位二进制全加器，做出仿真波形。

### 2、创建 Project

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526153116.png)

f_adder 项目创建成功。

### 3、创建 VHDL 文件

全加器由两个半加器和一个或门连接而成，

所以就需要先设计好半加器和或门，然后全加器来调用半加器和或门就可以了。

#### ①创建半加器 h_adder.vhd 文件

写入如下代码：

```vhdl
LIBRARY  IEEE;
USE IEEE.STD_LOGIC_1164.ALL;

ENTITY h_adder IS
  PORT ( a, b  : IN STD_LOGIC;
        co, so : OUT STD_LOGIC);
END ENTITY  h_adder;

ARCHITECTURE fh1 OF h_adder IS
BEGIN
  so <= NOT(a XOR (NOT b)) ;   co <= a AND b ;
END ARCHITECTURE fh1;

```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526154024.png)

#### ②创建或门 or2a.vhd 文件

写入如下代码：

```vhdl
LIBRARY  IEEE ;
 USE IEEE.STD_LOGIC_1164.ALL;
 
 ENTITY or2a IS
   PORT (a, b : IN STD_LOGIC;  c : OUT STD_LOGIC );
 END ENTITY or2a ;
 
 ARCHITECTURE one OF or2a IS
   BEGIN
   c <= a OR b ;
 END ARCHITECTURE one ;

```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526154510.png)

#### ③创建全加器 f_adder.vhd 文件

写入如下代码：

```vhdl
LIBRARY  IEEE;
 USE IEEE.STD_LOGIC_1164.ALL;
 
 ENTITY f_adder IS
   PORT (ain, bin,  cin : IN STD_LOGIC;
              cout, sum : OUT STD_LOGIC);
 END ENTITY f_adder;
 
 ARCHITECTURE fd1 OF f_adder IS
   COMPONENT h_adder
     PORT (  a, b : IN STD_LOGIC;
           co, so : OUT STD_LOGIC);
   END COMPONENT;
   COMPONENT or2a
      PORT (a, b : IN STD_LOGIC;
               c : OUT STD_LOGIC);
   END COMPONENT;

SIGNAL d, e, f : STD_LOGIC;
  BEGIN
   u1 : h_adder PORT MAP(a=>ain, b=>bin, co=>d, so=>e);
   u2 : h_adder PORT MAP(a=>e, b=>cin, co=>f, so=>sum);
   u3 : or2a    PORT MAP(a=>d, b=>f, c=>cout);
 END ARCHITECTURE fd1;

```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526154752.png)

#### ④编译

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526154953.png)

编译成功。

### 4、查看 RTL 图

如下:

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526155222.png)

查看完成。

### 5、仿真

写入测试数据如下：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526160616.png)

仿真结果：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200526160824.png)

符合设计要求，仿真成功。