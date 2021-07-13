# 1、Shell介绍

### Shell是什么？

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627162234.png)

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627162215.png)

# 2、快速使用

1. 新建一个shell脚本文件：vim myshell.sh

2. 开头输入：

   ```
   #!/bin/bash
   ```

3. 然后就可以输入需要执行的linux命令：

   ```
   echo hello,word!
   ls -l
   ```

4. 给myshell.sh文件执行权限：chmod 744 myshell.sh

5. 执行。

## 执行方式：

### 方式一：

使用绝对路径或相对路径：

- 绝对路径

  ```
  /home/azhu/myshell.sh
  ```

- 相对路径

  ```
  ./myshell.sh
  ```

### 方式二：

不推荐

**可以不用赋予执行的权限，直接执行：**

```
sh ./myshell.sh
```



# 3、shell变量

## 3.1介绍

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627163106.png)

- 系统变量
- 用户自定义的变量

## 3.2shell变量的定义

```
变量=值

name="azhu" 定义变量name

echo $name 使用变量name

unset name 撤销变量name

```

**变量定义规则：**

- 变量名称一般由字母、数字和下划线组成，但是不能以数字开头
- 等号两侧不能有空格
- 变量名称一般习惯为全大写

**将指令的返回值赋值给变量：**

- ```
  A=`ls -la` 使用反引号
  ```

- ```
  A=$(ls -la) 
  ```

## 3.3设置环境变量

```
export 变量名=变量值 
	将shell变量输出为环境变量
source 配置文件
	让修改后的配置信息立刻生效
echo $变量名
	使用环境变量
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627223845.png)

**使用：**

1. 在配置文件`/etc/profile`中定义环境变量`TOMCAT_HOME`：

   ```
   #添加以下代码
   export TOMCAT_HOME
   ```

2. 使配置文件生效：source /etc/profile

# 4位置参数设置

**什么是位置参数？**

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627224231.png)

**基本语法：**

```
$n n为数字，$0代表命令本身，$1-$9代表命令后面跟的第一到第九个参数，
	十以上的参数需要{}，${10}
$* 代表所有的参数，把所有参数当成一个整体
$@ 代表所有的参数，把每个参数区分对待
$# 代表命令行中参数的个数
```

# 5预定义参数

就是shell设计者事先已经定义好的变量，可以直接在shell脚本中使用。

**基本语法：**

```
$$ 当前进程的进程号pid
$! 后台运行的最后一个进程的进程号pid
$? 最后一次执行命令的返回状态，0正确执行，非0则不是正确执行
```

# 6运算符

**基本语法：**

```
$((运算表达式))
$[运算表达式]
expr m + n 运算符之间必须有空格

\* 乘
/  除
%  取余
```

# 7条件判断

**基本语法：**

```
[ 判断表达式 ] 注意判断表达式前后必须要有空格
```

**常用判断条件：**

```
= 字符串比较

-lt 小于
-le 小于等于
-eq 等于
-gt 大于
-ge 大于等于
-ne 不等于

文件权限：
-r 有读权限
-w 有写权限
-x 有执行权限

文件类型;
-f 文件存在并且是一个常规文件
-e 文件存在
-d 文件存在并且是一个目录
```

# 8流程控制

### if判断：

```
if[ 判断表达式 ];then
	程序
fi
```

或

```
if[ 判断表达式 ]
	then
		程序
	elif[ 判断表达式 ]
	then
		程序
fi
```

注意空格，推荐使用第二种。

### case选择：

```
case $变量名 in
	"值1")
	程序1
	;;
	
	"值2")
	程序2
	;;
	
	"值3")
	程序3
	;;
	
	*)
	默认程序
esac
```

### for循环：

语法1：

```
for 变量 in 值1 值2 值3 ...
do
	程序
done
	
例如：
for i in $NUM
do
	程序
done
```

语法2：

```
for((初始值;循环控制条件;变量变化))
do
	程序
done

例如：
for((i=0;i<10;i++))
do
	程序
done
```

### while循环：

```
while[ 判断表达式 ]
do
	程序
done
```

# 9读取控制台输入

基本语法：

```
read [选项] [参数]

-p 指定读取时的提示信息
-t 指定读取时等待的时间（秒）

参数：
变量名 指定读取值存入的变量名

例如;
read -p "请输入一个num：" NUM
```

# 10函数

- 系统函数
- 自定义函数

### 系统函数：

1、返回完整路径最后`/`的后面部分，常用于获取文件名

```
basename [pathname] [suffix]
	suffix为后缀，如果suffix指定了，basename 就会将pathname中的suffix去掉。
	
例如;
basename /home/azhu/a.txt
	输出：a.txt
basename /home/azhu/a.txt .txt
	输出：a
```

2、返回完整路径最后`/`的前面部分，常用于返回路径部分

```
dirname 文件绝对路径名

例如;
dirname /home/azhu/a.txt
	输出：/home/azhu
```

### 自定义函数：

```
定义函数：
[ function ]funname[()]
{
	程序
	[return int;]
}

调用函数;
funname [值]

例如：
function getSum(){
	SUM=$[$n1+$n2]
	echo $SUM
}

getSum $n1 $n2
```

# 11综合案例

**需求：**

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627232350.png)

思路：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627232403.png)

代码实现：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627232445.png)

在`crontab`中的配置：

```
10 2 * * * /usr/sbin/mysql_db_backup.sh
```



