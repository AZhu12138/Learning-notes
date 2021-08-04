## 问题

在idea里面，`import`自己写的类全部报红，但是类文件在项目是存在的。

## 原因

应该是更改了`JDK`的一些配置之后，导致idea的缓存出现问题。

## 处理

清一下idea的缓存，并重启。

1. File -> Invalidate Caches

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/Snipaste_2021-08-02_10-10-49.png)

2. 如下：

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/Snipaste_2021-08-02_11-03-04.png)

等待重启完成就可以啦！

