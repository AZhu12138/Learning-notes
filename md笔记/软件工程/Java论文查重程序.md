# 0、需求

题目：**论文查重**

描述如下：

**设计一个论文查重算法，给出一个原文文件和一个在这份原文上经过了增删改的抄袭版论文的文件，在答案文件中输出其重复率。**

- 原文示例：今天是星期天，天气晴，今天晚上我要去看电影。
- 抄袭版示例：今天是周天，天气晴朗，我晚上要去看电影。

要求输入输出采用文件输入输出，规范如下：

- 从**命令行参数**给出：论文原文的文件的**绝对路径**。
- 从**命令行参数**给出：抄袭版论文的文件的**绝对路径**。
- 从**命令行参数**给出：输出的答案文件的**绝对路径**。

测试样例使用方法是：orig.txt 是原文，其他 orig_add.txt 等均为抄袭版论文。

注意：答案文件中输出的答案为浮点型，精确到小数点后两位

| 项目                 | 内容                                                         |
| -------------------- | ------------------------------------------------------------ |
| 这个作业属于哪个课程 | [（信息安全1812）的链接](https://edu.cnblogs.com/campus/gdgy/informationsecurity1812) |
| 这个作业要求在哪里   | [（个人项目作业）作业要求的链接](https://edu.cnblogs.com/campus/gdgy/informationsecurity1812/homework/11155) |
| 这个作业的目标       | 设计一个论文查重算法，给出一个原文文件和一个在这份原文上经过了增删改的抄袭版论文的文件，在答案文件中输出其重复率。 |

# 1、前言

- **github地址**：https://github.com/AZhu12138/PaperCheck

## 1.1、开发环境

- 编程语言：Java 14

- IDE：Intellij IDEA 2020.1

- 项目构建工具：maven

- 单元测试：JUnit-4.12

- 性能分析工具：JProfiler 9.2

- 依赖的外部 jar 包：汉语言处理包

  ```xml
  <dependency>
      <groupId>com.hankcs</groupId>
      <artifactId>hanlp</artifactId>
      <version>portable-1.5.4</version>
  </dependency>
  ```

## 1.1、整体流程

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924133603.png)

## 1.2、类

- MainPaperCheck：main 方法所在的类
- HammingUtils：计算海明距离的类
- SimHashUtils：计算 SimHash 值的类
- TxtIOUtils：读写 txt 文件的工具类
- ShortStringException：处理文本内容过短的异常类

## 1.3、核心算法

- **simhash+海明距离**

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924135007.png)

- 具体可参考：

  [SimHash 原理与实现](https://www.cnblogs.com/jiyuqi/p/4845969.html)

# 2、接口的设计和实现

## 2.1、读写 txt 文件的模块

类：TxtIOUtils

包含了两个静态方法：

1、readTxt：读取txt文件

2、writeTxt：写入txt文件

实现：都是调用 Java.io 包提供的接口，比较简单，这里省略。

## 2.2、SimHash 模块（核心模块）

类：SimHashUtils

包含了两个静态方法：

1、getHash：传入String，计算出它的hash值，并以字符串形式输出，（使用了MD5获得hash值）

2、getSimHash：传入String，计算出它的simHash值，并以字符串形式输出，（需要调用 getHash 方法）

**getSimHash 是核心算法**，主要流程如下：

1、分词（使用了外部依赖 hankcs 包提供的接口）

```java
List<String> keywordList = HanLP.extractKeyword(str, str.length());//取出所有关键词
```

2、获取 hash 值

```java
String keywordHash = getHash(keyword);
if (keywordHash.length() < 128) {
    // hash值可能少于128位，在低位以0补齐
    int dif = 128 - keywordHash.length();
    for (int j = 0; j < dif; j++) {
        keywordHash += "0";
    }
}
```

3、加权、合并

```java
for (int j = 0; j < v.length; j++) {
    // 对keywordHash的每一位与'1'进行比较
    if (keywordHash.charAt(j) == '1') {
        //权重分10级，由词频从高到低，取权重10~0
        v[j] += (10 - (i / (size / 10)));
    } else {
        v[j] -= (10 - (i / (size / 10)));
    }
}
```

4、降维

```java
String simHash = "";// 储存返回的simHash值
for (int j = 0; j < v.length; j++) {
    // 从高位遍历到低位
    if (v[j] <= 0) {
        simHash += "0";
    } else {
        simHash += "1";
    }
}
```

## 2.3、海明距离模块

- 类：HammingUtils

包含了两个静态方法：

1、getHammingDistance：输入两个 simHash 值，计算出它们的海明距离 distance

```java
for (int i = 0; i < simHash1.length(); i++) {
    // 每一位进行比较
    if (simHash1.charAt(i) != simHash2.charAt(i)) {
        distance++;
    }
}
```

2、getSimilarity：输入两个 simHash 值，调用 getHammingDistance 方法得出海明距离 distance，在由 distance 计算出相似度。

```java
return 0.01 * (100 - distance * 100 / 128);
```

## 2.4、main 主模块

- main 方法的主要流程：

1. 从命令行输入的路径名读取对应的文件，将文件的内容转化为对应的字符串
2. 由字符串得出对应的 simHash值
3. 由 simHash值求出相似度
4. 把相似度写入最后的结果文件中
5. 退出程序

# 3、接口部分的性能改进

## 3.1、性能分析

- Overview

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924151947.png)

- 方法的调用情况

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924152035.png)

- 从分析图可以看到：

  调用次数最多的是com.hankcs.hanlp包提供的接口， 即分词、取关键词与计算词频花费了最多的时间。

  所以在性能上基本没有什么需要改进的。

# 4、单元测试

## 4.1、读写 txt 文件的模块 的测试

- 基本思路：

  1、测试正常读取

  2、测试正常写入

  3、测试错误读取

  4、测试错误写入

```java
public class TxtIOUtilsTest {
    @Test
    public void readTxtTest() {
        // 路径存在，正常读取
        String str = TxtIOUtils.readTxt("D:/test/orig.txt");
        String[] strings = str.split(" ");
        for (String string : strings) {
            System.out.println(string);
        }
    }
    @Test
    public void writeTxtTest() {
        // 路径存在，正常写入
        double[] elem = {0.11, 0.22, 0.33, 0.44, 0.55};
        for (int i = 0; i < elem.length; i++) {
            TxtIOUtils.writeTxt(elem[i], "D:/test/ans.txt");
        }
    }
    @Test
    public void readTxtFailTest() {
        // 路径不存在，读取失败
        String str = TxtIOUtils.readTxt("D:/test/none.txt");
    }
    @Test
    public void writeTxtFailTest() {
        // 路径错误，写入失败
        double[] elem = {0.11, 0.22, 0.33, 0.44, 0.55};
        for (int i = 0; i < elem.length; i++) {
            TxtIOUtils.writeTxt(elem[i], "User:/test/ans.txt");
        }
    }
}
```

- 测试结果：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200923172605.png)

- 代码覆盖率：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200923172723.png)

## 4.2、SimHash 模块 的测试

```java
public class SimHashUtilsTest {
    @Test
    public void getHashTest(){
        String[] strings = {"余华", "是", "一位", "真正", "的", "作家"};
        for (String string : strings) {
            String stringHash = SimHashUtils.getHash(string);
            System.out.println(stringHash.length());
            System.out.println(stringHash);
        }
    }
    @Test
    public void getSimHashTest(){
        String str0 = TxtIOUtils.readTxt("D:/test/orig.txt");
        String str1 = TxtIOUtils.readTxt("D:/test/orig_0.8_add.txt");
        System.out.println(SimHashUtils.getSimHash(str0));
        System.out.println(SimHashUtils.getSimHash(str1));
    }
}
```

- 测试结果：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200923205437.png)

- 代码覆盖率：

  （SimHashUtils 的 getHash 方法的异常 catch 测试不了）

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200923205510.png)

## 4.3、海明距离模块 的测试

- 部分代码：

```java
public class HammingUtilsTest {
    @Test
    public void getHammingDistanceTest() {
        String str0 = TxtIOUtils.readTxt("D:/test/orig.txt");
        String str1 = TxtIOUtils.readTxt("D:/test/orig_0.8_add.txt");
        int distance = HammingUtils.getHammingDistance(SimHashUtils.getSimHash(str0), SimHashUtils.getSimHash(str1));
        System.out.println("海明距离：" + distance);
        System.out.println("相似度: " + (100 - distance * 100 / 128) + "%");
    }
}

```

- 测试结果：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200923215732.png)

- 代码覆盖率：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200923215754.png)

## 4.4、主测试 MainTest

部分测试代码：

```java
public class MainTest {
    @Test
    public void origAndAllTest(){
        String[] str = new String[6];
        str[0] = TxtIOUtils.readTxt("D:/test/orig.txt");
        str[1] = TxtIOUtils.readTxt("D:/test/orig_0.8_add.txt");
        str[2] = TxtIOUtils.readTxt("D:/test/orig_0.8_del.txt");
        str[3] = TxtIOUtils.readTxt("D:/test/orig_0.8_dis_1.txt");
        str[4] = TxtIOUtils.readTxt("D:/test/orig_0.8_dis_10.txt");
        str[5] = TxtIOUtils.readTxt("D:/test/orig_0.8_dis_15.txt");
        String ansFileName = "D:/test/ansAll.txt";
        for(int i = 0; i <= 5; i++){
            double ans = HammingUtils.getSimilarity(SimHashUtils.getSimHash(str[0]), SimHashUtils.getSimHash(str[i]));
            TxtIOUtils.writeTxt(ans, ansFileName);
        }
    }
}
```

- 测试结果：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924004624.png)

  - 结果文件：

    ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924004705.png)

    ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924004748.png)

# 5、异常处理

## 5.1、设计与实现

当文本长度太短时，HanLp无法取得关键字，需要抛出异常。

```java
try{
    if(str.length() < 200) throw new ShortStringException("文本过短！");
}catch (ShortStringException e){
    e.printStackTrace();
    return null;
}
```

实现了一个处理这个异常的类：ShortStringException（继承了Exception）

```java
public ShortStringException(String message) {
        super(message);
    }
```

## 5.2、测试

- 测试结果：

  ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200924131305.png)

# 6、PSP 表格

| PSP2.1                                  | Personal Software Process Stages        | 预估耗时（分钟） | 实际耗时（分钟） |
| --------------------------------------- | --------------------------------------- | ---------------- | ---------------- |
| Planning                                | 计划                                    | 60               | 60               |
| · Estimate                              | · 估计这个任务需要多少时间              | 60               | 60               |
| Development                             | 开发                                    | 1120             | 1290             |
| · Analysis                              | · 需求分析 (包括学习新技术)             | 300              | 360              |
| · Design Spec                           | · 生成设计文档                          | 70               | 60               |
| · Design Review                         | · 设计复审                              | 30               | 30               |
| · Coding Standard                       | · 代码规范 (为目前的开发制定合适的规范) | 30               | 30               |
| · Design                                | · 具体设计                              | 90               | 90               |
| · Coding                                | · 具体编码                              | 300              | 360              |
| · Code Review                           | · 代码复审                              | 60               | 60               |
| · Test                                  | · 测试（自我测试，修改代码，提交修改）  | 240              | 300              |
| Reporting                               | 报告                                    | 240              | 250              |
| · Test Repor                            | · 测试报告                              | 60               | 70               |
| · Size Measurement                      | · 计算工作量                            | 60               | 60               |
| · Postmortem & Process Improvement Plan | · 事后总结, 并提出过程改进计划          | 120              | 120              |
|                                         | · 合计                                  | 1420             | 1600             |

# 7、参考

https://blog.csdn.net/lance_yan/article/details/10304747

https://www.cnblogs.com/huilixieqi/p/6493089.html

