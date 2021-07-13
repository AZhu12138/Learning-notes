# 一、EL 表达式

## 1、什么是 EL 表达式？

EL 表达式的全称是：Expression Language。是**表达式语言**。

EL 表达式的什么作用：EL 表达式主要是**代替 jsp 页面中的表达式脚本**在 jsp 页面中进行数据的输出。

因为 EL 表达式在输出数据的时候，要比 jsp 的表达式脚本要简洁很多。

EL 表达式的格式是：${ 表达式 } 

EL 表达式在输出 null 值的时候，输出的是空串。jsp 表达式脚本输出 null 值的时候，输出的是 null 字符串。

## 2、搜索域数据的顺序

EL 表达式主要是在 jsp 页面中输出数据。 主要是输出域对象中的数据。

当四个域中都有相同的 key 的数据的时候，EL 表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出。

## 3、EL 表达式输出 Bean

输出Bean的普通属性、数组属性、List集合属性、map 集合属性

## 4、运算

语法：${ 运算表达式 } ，EL 表达式支持如下运算符：

### （1）关系运算：大小等于

### （2）逻辑运算：与或非

### （3）算术运算：加减乘除取余

### （4）empty 运算

empty 运算可以判断一个数据是否为空，如果为空，则输出 true,不为空输出 false。

以下几种情况为空： 

1、值为 null 值的时候，为空 

2、值为空串的时候，为空 

3、值是 Object 类型数组，长度为零的时候 

4、list 集合，元素个数为零 

5、map 集合，元素个数为零

### （5）三元运算

表达式 1？表达式 2：表达式 3 

如果表达式 1 的值为真，返回表达式 2 的值，如果表达式 1 的值为假，返回表达式 3 的值。

### （6）“.”点运算 和 [ ] 中括号运算符

.点运算，可以输出 Bean 对象中某个属性的值。

[ ] 中括号运算，可以输出有序集合中某个元素的值。 

并且 [ ] 中括号运算，还可以输出 map 集合中 key 里含有特殊字符的 key 的值。

## 5、EL 表达式的 11 个隐含对象

EL 个达式中 11 个隐含对象，是 EL 表达式中自己定义的，可以直接使用。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813123841.png)

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813123923.png)

### （1）EL 获取四个特定域中的属性

- pageScope ====== pageContext 域 
- requestScope ====== Request 域 
- sessionScope ====== Session 域 
- applicationScope ====== ServletContext 域

### （2）pageContext 对象的使用

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813124440.png)

### （3） EL 表达式其他隐含对象的使用

param Map<String,String> 它可以获取请求参数的值 

paramValues Map<String,String[]> 它也可以获取请求参数的值，获取多个值的时候使用。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813124856.png)

header Map<String,String> 它可以获取请求头的信息 

headerValues Map<String,String[]> 它可以获取请求头的信息，它可以获取多个值的情况

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813124920.png)

cookie Map<String,Cookie> 它可以获取当前请求的 Cookie 信息

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813124944.png)

initParam Map<String,String> 它可以获取在 web.xml 中配置的 <context-param> 上下文参数

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813125005.png)

# 二、JSTL 标签库

JSTL 标签库 全称是指 JSPStandardTagLibrary JSP 标准标签库。是一个不断完善的开放源代码的 JSP 标 签库。

EL 表达式主要是为了替换 jsp 中的表达式脚本，而标签库则是为了替换代码脚本。这样使得整个 jsp 页面 变得更佳简洁

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813125236.png)

## 1、JSTL 标签库的使用步骤

（1）先导入 jstl 标签库的 jar 包。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200813125453.png)

（2）第二步，使用 taglib 指令引入标签库。

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

## 2、core 核心库使用

### （1）< c:set />（很少使用）

作用：set 标签可以往域中保存数据

```jsp
<c:set scope="session" var="abc" value="abcValue"/>
```

### （2） < c:if />

作用：if 标签用来做 if 判断。

```jsp
<c:if test="${ 12 == 12 }"> <h1>12 等于 12</h1> </c:if>
```

### （3） < c:choose >< c:when >< c:otherwise >

作用：多路判断。跟 switch...case....default 非常接近

```jsp
<c:choose> 
    <c:when test="${requestScope.height > 160}"> 
        <h3>大于 160</h3> 
    </c:when> 
    <c:when test="${requestScope.height > 150}"> 
        <h3>大于 150</h3> 
    </c:when> 
    <c:when test="${requestScope.height > 140}">
        <h3>大于 140</h3> 
    </c:when>
    <c:otherwise> 其他小于 140 </c:otherwise> 
</c:choose>
```

### （4）< c:forEach />

作用：遍历输出使用。

① 遍历 1 到 10，输出

```jsp
<table border="1">
    <c:forEach begin="1" end="10" var="i">
        <tr>
            <td>第${i}行</td>
        </tr>
    </c:forEach>
</table>
```

② 遍历 Object 数组

```jsp
<%
request.setAttribute("arr", new String[]{"18610541354","18688886666","18699998888"});
%>
<c:forEach items="${ requestScope.arr }" var="item">
    ${ item } <br>
</c:forEach>
```

③ 遍历 Map 集合

```jsp 
<%
Map<String,Object> map = new HashMap<String, Object>(); 
map.put("key1", "value1"); 
map.put("key2", "value2"); 
map.put("key3", "value3");  
%> 
<c:forEach items="${ requestScope.map }" var="entry"> 
    <h1>${entry.key} = ${entry.value}</h1> 
</c:forEach>
```

④ 遍历 List 集合

list 中存放 Student 类，有属性：编号，用户名，密码，年龄，电话信息

```jsp
<c:forEach begin="2" end="7" step="2" varStatus="status" items="${requestScope.stus}" var="stu"> 
    <tr> 
        <td>${stu.id}</td>
        <td>${stu.username}</td>
        <td>${stu.password}</td>
        <td>${stu.age}</td>
        <td>${stu.phone}</td>
        <td>${status.step}</td>
    </tr>
</c:forEach> 
```