# HttpClient是什么？

HTTP 协议是 Internet 上使用得最多、最重要的协议之一，越来越多的 Java 应用程序需要直接通过 HTTP 协议来访问网络资源。

虽然在 JDK 的 java net包中已经提供了访问 HTTP 协议的基本功能，但是对于大部分应用程序来说，JDK 库本身提供的功能还不够丰富和灵活。

HttpClient 是 Apache Jakarta Common 下的子项目，用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。

HttpClient 已经应用在很多的项目中，比如 Apache Jakarta 上很著名的另外两个开源项目 Cactus 和 HTMLUnit 都使用了 HttpClient。

**简单来说就是：**

-  JDK 的 java net包中有进行HTTP连接的功能，但是功能不够丰富和灵活。
- HttpClient 是一个Apache 的工具包。
- HttpClient 比  JDK 的 java net包的功能更加丰富和灵活。

## 主要的功能有：

（1）实现了所有 HTTP 的方法（GET,POST,PUT,HEAD 等）

（2）支持自动转向

（3）支持 HTTPS 协议

（4）支持代理服务器 等

## 说明一下：

**HttpClient 和 HttpComponents 的关系？**

我们先来看看 Apache 官网对两者的描述：

> Commons HttpClient 项目现已结束，不再开发。它已被 HttpClient 和 HttpCore 模块中的 Apache HttpComponents 项目取代，提供更好的性能和更大的灵活性。

> Apache HttpComponents 项目负责创建和维护专注于 HTTP 和相关协议的低级 Java 组件工具集。该项目在 Apache 软件基金会下运作，是更大的开发人员和用户社区的一部分。

**简单来说就是：**

1. 两个都是 Apache 的项目
2. 一开始是只有 HttpClient ，但是慢慢发展到现在就已经被废弃了，取而代之是 HttpComponents 。
3. HttpComponents 比 HttpClient 更加强大，
   - HttpComponents 包含有更好的 HttpClient（`HttpComponents Client`）
   - 除了 HttpClient，还包含有：`HttpComponents Core` 和 `Commons HttpClient (legacy)`。
4. 所以我们应该使用 HttpComponents 中的更好的 HttpClient（正如下文所展示的那样）。



# 如何使用？

首先，导包：

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.8</version>
</dependency>
```

然后无论是发`GET`还是`POST`请求，使用起来最主要的步骤基本都是：

1. 创建`HttpClient`对象
2. 创建`HttpGet`或者`HttpPost`对象
3. 通过`HttpGet`或者`HttpPost`对象设置请求头或请求参数等信息
4. 通过`HttpClient`对象来执行`HttpGet`或者`HttpPost`对象，同时返回`HttpResponse`对象
5. 通过`HttpResponse`对象可以获取到响应头和响应体的信息
6. 关闭资源：关闭`HttpClient`对象和`HttpResponse`对象。

就是如此的简单。

下面看实际的代码：

### GET方法：

```java
@Test
public static void doGet() {
    String url = "http://baiu.com/abc?key1=name&key2=name2";
    // 1.创建httpclient
    CloseableHttpClient httpClient = HttpClients.createDefault();
    // 2.创建HttpGet
    HttpGet httpGet = new HttpGet(url);
    try {
        // 3.设置请求参数信息
        httpGet.setHeader("Accept-Language", "zh-cn,zh;q=0.5");
        // 4.请求执行，获取响应
        CloseableHttpResponse response = httpClient.execute(httpGet);
        // 5.获取响应实体
        String result = EntityUtils.toString(response.getEntity(), "UTF-8");
        System.out.println(result);
        // 6.关闭资源
        response.close();
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        try {
            // 6.关闭资源
            httpClient.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### POST方法：

```java
@Test
public static void doPost() {
    String url = "http://baiu.com/abc";
    // 1.创建httpClient
    CloseableHttpClient httpClient = HttpClients.createDefault();
    // 2.创建httpPost
    HttpPost httpPost = new HttpPost(url);
    // 3.1创建参数列表
    List formParams = new ArrayList();
    formParams.add(new BasicNameValuePair("username", username));
    formParams.add(new BasicNameValuePair("userpass", userpass));
    formParams.add(new BasicNameValuePair("useryzm", useryzm));
    try {
        // 3.2设置请求参数信息
        UrlEncodedFormEntity urlEncodedFormEntity = new UrlEncodedFormEntity(formParams, "GBK");
        httpPost.setEntity(urlEncodedFormEntity);
        // 4.请求执行，获取响应
        CloseableHttpResponse response = httpClient.execute(httpPost);
        // 5.获取响应实体
        String result = EntityUtils.toString(response.getEntity(), "UTF-8");
        System.out.println(result);
        // 6.关闭资源
        response.close();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            // 6.关闭资源
            httpClient.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```



# 有中文参数怎么办？

众所周知：GET请求的参数是显示在`url`中的，而POST请求的参数是在请求体里面的，不会显示在`url`上。

所以，就可以分为两种情况：

1. GET请求的`url`中有中文参数
2. POST请求的请求体里面有中文参数



### GET请求的url中有中文参数

很简单，在参数拼接到`url`之前，先把参数进行编码。

```java
String newName1 = URLEncoder.encode(oldName1, "GBK")
```

然后才拼接成一个完整的`url`

```java
String newUrl = oldUrl + "?" + "key1=" + newName1 + "&key2=" + oldName2 ;
```

然后就可以拿着这个`newUrl`进行GET请求了。

**注意：**

- 是对带有中文的参数进行单独的`encode`，而不是对整个`url`进行`encode`。
- encode的编码方式需要根据服务端来选择，大多数为`UTF-8`，我这里比较特殊是`GBK`。

> 另：URLEncoder 类是 JDK自带的，可以直接使用。
>
> ```java
> import java.net.URLEncoder;
> ```



### POST请求的请求体里面有中文参数

这种情况就更加的简单，只需要在设置请求体的参数时，指定编码方式就可以了：

```java
// 3.2设置请求参数信息
UrlEncodedFormEntity urlEncodedFormEntity = new UrlEncodedFormEntity(formParams, "GBK");
httpPost.setEntity(urlEncodedFormEntity);
```

**注意：**编码方式需要根据服务端来选择，大多数为`UTF-8`，我这里比较特殊是`GBK`。



# 有xml格式的参数怎么办？

其实不管是`xml`格式的参数还是中文参数，

对于HTTP请求来说都是非法字符，

所有的非法字符都可以使用上面处理中文参数的方法来解决。

所以不论是GET还是POST，参照上述方法处理就行了。

# 最后

如果想了解更加深入的`HttpClient `知识，可以到官网查看详细的教程：

http://hc.apache.org/httpcomponents-client-4.5.x/current/tutorial/html/ 

