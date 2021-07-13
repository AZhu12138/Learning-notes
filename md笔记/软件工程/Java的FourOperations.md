

| 项目                 | 内容                                                         |
| -------------------- | ------------------------------------------------------------ |
| 这个作业属于哪个课程 | [（信息安全1812）的链接](https://edu.cnblogs.com/campus/gdgy/informationsecurity1812) |
| 这个作业要求在哪里   | [（结对项目）作业要求的链接](https://edu.cnblogs.com/campus/gdgy/informationsecurity1812/homework/11157) |
| 这个作业的目标       | 实现一个自动生成小学四则运算题目的命令行程序，熟悉结对开发项目的流程。 |

# 1、前言

**github地址**：[123]()

**合作“对友”：**

| 姓名 | 学号       |
| ---- | ---------- |
| 朱亮 | 3118005393 |
| 123  | 123        |



# 2、设计实现过程

设计包括代码如何组织，比如会有几个类，几个函数，他们之间关系如何，关键函数是否需要画出流程图？（5分）

## 2.1、pojo 实体类

### 2.1.1、Problem

```java
/**
 * Problem 题目实体类，包含题目本身及其对应的答案
 */
public class Problem {
    private String suffixProblem;
    private String infixProblem;
    private String answer;
}
```

### 2.1.2、OperationStack

```java
/**
 * 用于实现四则运算的栈数据结构
 */
public class OperationStack {
    String[] data;
    int maxSize;
    int top;
}
```

## 2.2、生成随机操作数模块（OperandUtils）

```java
/**
 * 生成随机操作数
 */
public class OperandUtils {

    /**
     * 生成一个随机的操作数
     * @param range 操作数的范围
     * @return 随机的操作数
     */
    public static String getRandomOperand(int range) {
        // 分子
        int numerator;
        // 分母
        int denominator;
        Random random = new Random();

        denominator = random.nextInt(15) + 1;
        numerator = random.nextInt(denominator * range);
        String fraction = numerator + "/" + denominator;

        return fraction;
    }
}
```

## 2.3、生成随机操作符模块（OperatorUtils）

```java
/**
 * 生成随机操作符
 */
public class OperatorUtils {
    /**
     * 生成随机操作符
     * @return 随机操作符
     */
    public static String getRandomOperator() {
        ...
    }
    /**
     * 获得操作符的优先级
     * @param operator 传入的操作符
     * @return 返回优先级
     */
    public static int getOperatorOrder(String operator) {
        ...
    }
    /**
     * 检查参数是否含有运算符
     * @param param 传入的参数
     * @return 返回结果
     */
    public static boolean hasOperator(String param) {
        ...
    }
}
```



# 3、代码说明

展示出项目关键代码，并解释思路与注释说明。（4分）



# 4、测试运行

共享你对程序进行测试的至少10个测试用例，以及说明为什么你能确定你的程序是正确的。（3分）

## 4.1、pojo 实体类的测试

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201009224616.png)

## 4.2、生成随机操作数模块的测试

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201009234209.png)

## 4.3、生成随机操作符模块的测试

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201010105449.png)

# 5、效能分析

记录你在改进程序性能上花费了多少时间，描述你改进的思路，并展示一张性能分析的图。如果可能，展示你程序中消耗最大的函数。（3分）



# 6、PSP表格

在开始实现程序之前，PSP表格(参加附录1)记录下你估计将在程序的各个模块的开发上耗费的时间。（1分）

在你实现完程序之后，在PSP表格记录下你在程序的各个模块上实际花费的时间。（1分）



# 7、项目小结

结对项目总结成败得失，分享经验，总结教训。两个人共同撰写一个博客，包含上述内容的描述，同时包含结对感受，以及两个人对彼此结对中的闪光点或建议的分享。（2分)

