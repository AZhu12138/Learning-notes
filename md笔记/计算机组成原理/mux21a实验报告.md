#### 1、实验目标

通过本实验，掌握使用VHDL硬件描述语言设计一个mux21a二选一选择器，做出仿真波形。

#### 2、创建 Project

File->New 如下图：选 New Quartus Prime Project

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589353824466.png" style="zoom: 67%;" />

OK 之后：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589353997556.png" style="zoom:67%;" />

之后就如下图：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589355895035.png" style="zoom:67%;" />

#### 3、创建 VHDL 文件

File->New 如下图：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589356051888.png" style="zoom:67%;" />

OK 之后如下图：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589356200689.png" style="zoom:67%;" />

写入代码如下：

```vhdl
LIBRARY  IEEE ;
USE IEEE.STD_LOGIC_1164.ALL;

ENTITY mux21a IS
  PORT (a, b, s : IN STD_LOGIC;
              y : OUT STD_LOGIC);
END ENTITY mux21a;
ARCHITECTURE one OF mux21a IS
   BEGIN
	  y <= a  WHEN  s = '0'  ELSE  b;

END ARCHITECTURE one ;

```

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589357698995.png" style="zoom:67%;" />

编译成功：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589357743338.png" style="zoom:67%;" />

#### 4、查看 RTL图

如下：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589358143485.png" style="zoom:67%;" />

如下：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589358229731.png" style="zoom:67%;" />

查看完毕。

#### 5、仿真

File->New 如下：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589358442467.png" style="zoom:67%;" />

OK之后：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589358602710.png" style="zoom:67%;" />

之后：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589358696573.png" style="zoom:67%;" />

再：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589359995434.png" style="zoom:67%;" />

仿真后：

<img src="https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/1589360124452.png" style="zoom:67%;" />

符合设计要求，仿真完成。
