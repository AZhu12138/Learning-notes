# 一、MySQL的Blob类型

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200721140215.png)

# 二、插入

1、图片 保存 在idea的项目的某个位置中

2、new File("playgirl.jpg")中的 playgirl.jpg 为 **相对路径**。

```java
        FileInputStream inputStream = new FileInputStream(new File("playgirl.jpg"));
        ps.setBlob(4, inputStream);
```

> 删除：与Blob类型无关
>
> 修改：与“插入”操作类似

# 三、查找

- 其它代码与普通的通用查询一致

文件保存到了idea项目中：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200721142639.png)

另外的资源关闭：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200721142832.png)

