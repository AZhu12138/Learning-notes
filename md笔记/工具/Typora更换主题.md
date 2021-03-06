# Typora更换主题详解

## 更换Typora自带的主题

很简单：

1. 打开Typora

2. 点击左上角“主题”

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210805111335.png)

3. 就可以选择你想要的主题啦！

## 主题太少没有喜欢的？

那就去Typora官网下载更多的主题：

https://theme.typora.io/

具体步骤：

1. 点击链接去下载：https://theme.typora.io/

2. 下载下来文件一般有两种：（假设主题名字为`abc`）

   - 一个名为"abc"的文件夹
   - 一个`abc.css`文件
   - 如果还有其他文件或者文件夹，是没用的，不管它。

3. 打开Typora，左上角`文件`->`偏好设置`->`外观`->`主题`->`打开主题文件夹`

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210805111042.png)

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210805111519.png)

4. 然后把：

   - 一个名为"abc"的文件夹
   - 一个`abc.css`文件

   复制到该文件夹下。

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210805111734.png)

5. 关闭Typora，再打开Typora，点击左上角“主题”，

6. 这样就可以愉快地使用你喜欢的主题啦！

**注意：**

"abc"的这个文件夹有的主题可能会没有，就只有一个`.css`文件。

这个没关系，因为这个文件夹是用来存储字体文件的，没有这个文件夹说明它主题的字体是使用**默认字体**或者用**系统自带字体**或者从**网上加载字体**。

那就只需要复制`.css`文件就可以了。

## 主题太没特色还想自定义？

**当然可以！**

相信十分细心的你已经发现了**主题的核心就是一个`.css`文件而已**。

因为我们的`markdown`语法实际上是和`html`标签具有一一对应关系的。

所有Typora本质上只是一个将`markdown`语法转换`html`标签，再将`html`标签转换为可视化内容的**浏览器**。

而主题就是通过一个`.css`文件来实现渲染样式的。

最后，`css`对于聪明的你来说肯定是易如反掌啦！

接下来的操作请参考：

https://blog.csdn.net/weixin_44911685/article/details/119449641

