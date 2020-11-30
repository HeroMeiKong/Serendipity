# 从URL输入到页面展现发生了什么——简单理解版

## 一、在浏览器中输入URL（统一资源定位符）

[统一资源定位符](https://baike.baidu.com/item/url)是对可以从互联网上得到的资源的位置和访问方法的一种简洁的表示，是互联网上标准资源的地址。互联网上的每个文件都有一个唯一的URL，它包含的信息指出文件的位置以及浏览器应该怎么处理它。

**URL一般格式：**
>协议类型://<主机名>:<端口>/<路径>/文件名

**URL完整格式(带方括号[]的为可选项)：**
>protocol://hostname[:port]/path/[;parameters][?query]#fragment

### 格式说明

#### protocol(协议)

- 常见协议：
  - file 资源是本地计算机上的文件。格式file:///，注意后边应是三个斜杠。
  - ftp 通过 FTP访问资源。格式 FTP://
  - http 通过 HTTP 访问该资源。 格式 HTTP://
  - https 通过安全的 HTTPS 访问该资源。 格式 HTTPS://

#### hostname(主机名)

    是指存放资源的服务器的[域名系统](https://baike.baidu.com/item/%E5%9F%9F%E5%90%8D%E7%B3%BB%E7%BB%9F)[(DNS)](https://baike.baidu.com/item/DNS/427444) 主机名或 IP 地址。有时，在主机名前也可以包含连接到服务器所需的用户名和密码(格式：username:password@hostname）

#### port(端口号)

    整数，可选，省略时使用方案的默认端口，各种[传输协议](https://baike.baidu.com/item/%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)都有默认的端口号，如http的默认端口为80。如果输入时省略，则使用默认端口号。有时候出于安全或其他考虑，可以在服务器上对端口进行重定义，即采用非标准端口号，此时，URL中就不能省略端口号这一项。

- 部分协议端口号：
    协议类型|默认端口
    :-: | :-:
    http|80
    ftp|21
    https|443
    telnet|23

#### path(路径)

    由零或多个“/”符号隔开的字符串，一般用来表示主机上的一个目录或文件地址。

#### parameters(参数)

    这是用于指定特殊参数的可选项。

#### query(查询)

    可选，用于给[动态网页](https://baike.baidu.com/item/%E5%8A%A8%E6%80%81%E7%BD%91%E9%A1%B5)(如使用CGI、ISAPI、PHP/JSP/ASP/ASP.NET等技术制作的网页)传递参数，可有多个参数，用“&”符号隔开，每个参数的名和值用“=”符号隔开。

#### fragment(信息片断)

    字符串，用于指定网络资源中的片断。例如一个网页中有多个名词解释，可使用fragment直接定位到某一名词解释。

## 二、[域名解析](https://baike.baidu.com/item/%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90)

**域名解析是把[域名](https://baike.baidu.com/item/%E5%9F%9F%E5%90%8D)指向网站空间IP，让人们通过注册的域名可以方便地访问到网站的一种服务。[IP地址](https://baike.baidu.com/item/IP%E5%9C%B0%E5%9D%80)是网络上标识站点的数字地址，为了方便记忆，采用域名来代替IP地址标识站点地址。域名解析就是域名到IP地址的转换过程。域名的解析工作由[DNS服务器](https://baike.baidu.com/item/DNS%E6%9C%8D%E5%8A%A1%E5%99%A8)来完成。**

**DNS存在多级缓存：**

1. 查看[浏览器缓存](https://baike.baidu.com/item/%E6%B5%8F%E8%A7%88%E5%99%A8%E7%BC%93%E5%AD%98)
2. 查看[系统缓存](https://baike.baidu.com/item/%E7%B3%BB%E7%BB%9F%E7%BC%93%E5%AD%98)
3. 查看[路由器](https://baike.baidu.com/item/%E8%B7%AF%E7%94%B1%E5%99%A8)缓存
4. 查看[ISP(Internet Service Provider)](https://baike.baidu.com/item/isp/10152)DNS缓存
5. 没找到，将[迭代查询](https://baike.baidu.com/item/%E8%BF%AD%E4%BB%A3%E6%9F%A5%E8%AF%A2)访问[根域名服务器](https://baike.baidu.com/item/%E6%A0%B9%E5%9F%9F%E5%90%8D%E6%9C%8D%E5%8A%A1%E5%99%A8)缓存、顶级域名服务器缓存、主域名服务器缓存。
![图1.jpg](http://upload-images.jianshu.io/upload_images/5999132-c741e0506a23cf85.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图2.gif](http://upload-images.jianshu.io/upload_images/5999132-e1509fc1ecaab9c7.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三、服务器处理

**常见服务器：[Apache](https://baike.baidu.com/item/apache/6265)、[Nginx](https://baike.baidu.com/item/nginx)、[IIS](https://baike.baidu.com/item/iis/99720)、[Lighttpd](https://baike.baidu.com/item/lighttpd) 等等**

## 四、浏览器处理

>1 .加载
2 .解析
3 .渲染
4 .重绘
>>![Webkit 主流程.jpg](http://upload-images.jianshu.io/upload_images/5999132-e7e7c0d9cb29778a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Mozilla 的 Gecko 渲染引擎主流程.jpg](http://upload-images.jianshu.io/upload_images/5999132-681b751ac662398a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
[了解详情：浏览器工作原理详解](http://blog.csdn.net/dangnian/article/details/50876241)

### 参考文章

[URL格式](https://baike.baidu.com/item/URL%E6%A0%BC%E5%BC%8F)

[前端面试题：从url到页面展现，这之中发生了什么？](https://www.jianshu.com/p/fc957e25102e)

[前端经典面试题: 从输入URL到页面加载发生了什么？](https://link.jianshu.com/?t=https://segmentfault.com/a/1190000006879700)

[浏览器工作原理详解](http://blog.csdn.net/dangnian/article/details/50876241)

[What really happens when you navigate to a URL](https://link.jianshu.com/?t=http://igoro.com/archive/what-really-happens-when-you-navigate-to-a-url/)

[从URL输入到页面展现](https://link.jianshu.com/?t=http://book.jirengu.com/jrg-team/frontend-knowledge-ppt/www/%E5%89%8D%E7%AB%AF%E5%85%A5%E9%97%A8-%E4%BB%8E%20URL%E8%BE%93%E5%85%A5%E5%88%B0%E9%A1%B5%E9%9D%A2%E5%B1%95%E7%8E%B0.html#/)

### 纯属个人简单理解，望大佬些细心指出错误或不足之处！！！
