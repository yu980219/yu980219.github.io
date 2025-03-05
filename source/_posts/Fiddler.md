---
title: Fiddler基础教程
tags: [fiddler,网络]
categories:
  - 其他
  - 逆向
index_img: /img/fiddler.jpg
date: 2025/03/05
---

配置Fiddler教程：https://blog.csdn.net/2401_84182665/article/details/138395109

## 一、Fiddler简介

- Fiddler是位于客户端和服务器端的HTTP代理
- 目前最常用的nttp抓包工具之一
- 功能非常强大，是web调试的利器
  - 监控浏览器所有的HTTP/HTTPS流量查看、分析请求内容细节
  - 伪造客户端请求和服务器响应
  - 测试网站的性能
  - 解密HTTPS的web会话
  - 全局、局部断点功能
  - 第三方插件
- 场景使用场景
  - 接口调试、接口测试、线上环境调试、web性能分析
  - 判断前后端bug、开发环境hosts配置、mock、弱网断网测试

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112041987.png)

## 二、Fiddler原理

启动fiddler时，会设置一个系统代理，代理端口是8888

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112049726.png)

如何验证？

打开谷歌浏览器

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112113938.png)

如果把fiddler关掉，再看代理服务器，就会被关闭

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112215974.png)

这就是为什么fiddler可以抓到浏览器的包

## 三、HTTP协议

- Hyper Text Transfer Protocol(超文本传输协议)
- 用于从万维网服务器传输超文本（资源）到本地浏览器的传送协议
- HTTP协议是基于TCP的应用层（高层协议）协议，它不关心数据传输的细节，主要是用来规定客户端和服务端的数据传输格式，最初是用来向客户端传输HTML页面的内容。默认端口是80
- http是基于请求与响应模式的、无状态的、应用层的协议



请求百度，并将资源保存为txt

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112221584.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250305104808720.png)

### 3.1 HTTP请求报文

HTTP请求报文主要由请求行、请求头部、空一行、请求正文4部分组成

前面三个是一定有的，但是请求正文不一定会有。

**请求报文：**

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112226104.png)



#### 请求方法（Request Method）

| 请求方法 | 备注                                 |
| -------- | ------------------------------------ |
| GET      | 请求资源                             |
| POST     | 提交资源                             |
| Head     | 获取响应头                           |
| PUT      | 替换资源                             |
| DELETE   | 删除资源                             |
| OPTIONS  | 允许客户端查看服务器的性能           |
| TRACE    | 回显服务器收到的请求，用于测试或诊断 |

#### URL

- Uniform Resource Locator：统一资源定位符
  - 用于描述网上的资源
- 格式：schema://host[:port#]/path/.../[?query-string]
  - scheme：协议，如http，https，ftp等
  - host：域名或者IP地址
  - port：端口
  - path：资源路径
- query-string：发送的参数
  - 如：https://www.baidu.com/s?wd=柠檬班

#### 请求头（Request Header）


| 请求头           | 描述                                                                 |
|------------------|--------------------------------------------------------------------|
| Host             | 主机IP地址或域名                                                   |
| User-Agent       | 客户端相关信息，如操作系统、浏览器等信息                             |
| Accept           | 指定客户端接收信息类型，如`image/jpg`、`text/html`、`application/json` |
| Accept-Charset   | 客户端接受的字符集，如`gb2312`、`iso-8859-1`                        |
| Accept-Encoding  | 可接受的内容编码，如`gzip`                                          |
| Accept-Language  | 接受的语言，如`Accept-Language:zh-cn`                               |
| Authorization    | 客户端提供给服务端，进行权限认证的信息                               |
| Cookie           | 携带的Cookie信息                                                   |
| Referer          | 当前文档的URL，即从哪个链接过来的                                   |
| Content-Type     | 请求体内容类型，如`Content-Type:application/x-www-form-urlencoded`   |
| Content-Length   | 数据长度                                                           |
| Cache-Control    | 缓存机制，如`Cache-Control:no-cache`                                |
| Pragma           | 防止页面被缓存，和`Cache-Control:no-cache`作用一样                   |

### 3.2 HTTP响应报文

HTTP响应报文主要由状态行、消息报头、空一行、响应正文4部分组成

**响应报文：**

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112231897.png)

#### 状态码(Status Code)

用以表示网页服务器http响应状态的3位数字代码

| 状态码 | 描述                     |
| ------ | ------------------------ |
| 1xx    | 提示信息，请求被成功接收 |
| 2xx    | 成功，请求被成功处理 200 |
| 3xx    | 重定向相关 304           |
| 4xx    | 客户端错误 404           |
| 5xx    | 服务器端错误 500         |

#### 响应头(Response Header)

| 响应头         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| Server         | HTTP服务器的软件信息                                         |
| Date           | 响应报文的时间                                               |
| Expires        | 指定缓存过期时间                                             |
| Set-Cookie     | 设置Cookie                                                   |
| Last-Modified  | 资源最后修改时间                                             |
| Content-Type   | 响应的类型和字符集，如`Content-Type:text/html;charset=utf-8` |
| Content-Length | 内容长度                                                     |
| Connection     | 如`Keep-Alive`，表示保持TCP连接不关闭，不会永久保持连接，服务器可设置 |
| Location       | 指明重定向的位置，新的URL地址，如304的情况                   |

## 四、FIddler工具栏

### 添加备注

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112236179.png)

### 重放（快捷键R）

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112239260.png)

重放多次，按住shift+r，可以设置重放的次数

### 删除（可根据条件删除）

快捷键：ctrl+x

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112243199.png)

### 解码

对所有请求做解码

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112246492.png)

### 保存会话的数量

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112249111.png)

### 选择要监听的程序

按住拖动到应用上

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112252081.png)

### 查找

快捷键：ctrl+f

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112254627.png)

### 保存

保存所有的会话保存为saz后缀的文件

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112257975.png)

### 快捷打开浏览器

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112300510.png)

### 清除浏览器缓存

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112303391.png)

### 编码解码工具

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112306625.png)

### 本机信息

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112309470.png)

## 五、会话列表

### Session List(会话列表)

- Fiddler抓取到的每条http请求(每一条称为一个session)
- 主要包含了请求的ID编号、状态码、协议、主机名、URL、内容类型、body大小、进程信息、自定义备注等信息

1. 【#】HTTP Request的顺序从 1开始，技照页面加载请求的顺序递增
2. 【Result】HTTP 响应的状态，可以参考这里。
3. 【Protocol】读求使用的协议(如 HTTP/HTTPS/FTP)
4. 【Host】请求地址的城名
5. 【URL】请求的服务器路径和文件名，也包括 GET @数
6. 【BODY】读求的大小，以 byte 为单位
7. 【Caching】请末的续存过期时间或缓存控制 header 等值
8. 【Content-Type】请求响应的类型(Content-Type)
9. 【Process】发出此清求的 Windows 进程及进程 ID
10. 【Comments】用户通过脚本或者右健菜单给此 session 增加的备注
11. 【Custom】用户可以通过脚本设置的自定义值

## 六、命令行

### QUICK EXEC

可输入HELP，查看所有命令。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112314836.png)

### 切换是否作为系统代理

点击之后，fiddler就不作为系统代理了

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112318011.png)

### 断点

点击一次是请求前断点

点击两次是响应后断点：返回后还没有发给客户端

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112321594.png)

### 会话数量

会话数量以及选择了几个

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112324741.png)

### 附加信息

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112328636.png)

## 七、辅助工具

### 统计

HTTP请求的性能和其他数据分析，如DNS解析的时间，建立TCP/IP连接的时间消耗等信息

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112331372.png)

### 检查器

用非常多的形式，展示请求报文和响应报文

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112334828.png)

### * AutoResponder（自动响应器）（重要）

AutoResponder可用于拦截某一请求，进行如下操作:

- 重定向到本地的资源
- 使用Fiddler的内置响应

- 自定义响应

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112338330.png)

比如想更换网页上的这张图片，右键复制这张图片的地址

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112340970.png)

给它来一个内置的404响应

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112343918.png)

再次请求页面，发现图片被拦截

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112347211.png)

除了内置的响应，可以自定义文件来替换，让响应不从服务器回来，而是从本地返回

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112350168.png)

选择好文件，点击save，图片被更换

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112352816.png)

**除此之外，还可以替换js文件等，这对生产环境的调试很有帮助！**

#### 应用场景（测试接口）：

拖动左侧响应到右侧来

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112356861.png)

右键，编辑响应

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112359911.png)

更改返回体内容，点击save

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112402437.png)

可以进行极端测试，比如测试响应体什么都不填的时候，前端页面时什么样子的

**PS：使用其他功能时，记得关闭该选项 enable**

### Composer（设计者）

可以模拟请求，点击Execute

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112405452.png)

也可以拖动左侧的请求过来，它会把这些参数（请求头和请求体等）都放到composer中，以便做一些接口测试。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112408139.png)

**可以验证一些后端的数据格式的校验，以防用户绕过前端而通过后端的请求**

**也可以抓到很多请求拖动过来，进行重放（可以批量拖动），连点三下，可以选择一部分请求进行execute**

### Filters（过滤器）

点击对号，开启过滤

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112411592.png)

**PS：使用其他功能时，记得关闭该选项**

#### 只展示局域网的请求或网络上的请求

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112414687.png)

#### 只展示某主机下的请求

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112418120.png)

#### 只展示某个进程下的请求

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112421209.png)

#### 只展示/隐藏url包含了什么

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112426528.png)

#### 只展示/隐藏header包含了什么

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112429314.png)

#### 删除所有的请求的某个header

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112431900.png)

#### 给所有的请求设置header

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112434572.png)

#### 为某个条件设置断点

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112439759.png)

#### 过滤某个响应状态码的请求

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112442278.png)

#### 过滤某个响应类型或响应内容大小的请求

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112444706.png)

#### 阻塞js、css、image等请求

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112447111.png)

#### 过滤或设置响应

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112449511.png)

## 八、断点

### 全局断点

设置全局的请求前断点，或响应后断点

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112452009.png)

或者点击命令行下方空白处设置断点

请求前断点：可以在发送请求前进行数据篡改（头部信息等）

响应后断点：得到响应后可以修改返回体

（可以让返回数据完全没有，进行一些极端测试）

（也可以模拟网络中断，响应体被fiddler拦截，客户端迟迟收不到响应，看会做出什么响应）

### 局部断点

**一般情况下，使用局部断点，而不用全局断点，全局断点会拦截所有请求**

1. 在命令行使用局部断点（请求前断点）

   bpu 内容

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112454882.png)

   再次输入bpu，取消断点

2. 在命令行使用局部断点（响应后断点）

   bpafter 内容

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112457630.png)

   再次输入bpu，取消断点

## 九、弱网测试

勾选，模拟网络限速

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112459842.png)

### 原理：

点击按钮

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112502227.png)

搜索simulate，代码中每请求1KB延迟了300ms，每响应1KB延迟150ms

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112507084.png)

可以根据需求设置弱网程度

## 十、HTTPS和APP抓包

- 点击Tools >Options >HTTPS
- 勾选Decrypt HTTPs Traffic

设置好之后，即可抓取https请求

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112509824.png)

如果开启之后还是抓不到，重置删除所有证书

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112512309.png)

弹出安装证书选项，安装证书。

判断是否安装成功，打开windows证书管理器

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112515218.png)

查找证书

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112517505.png)

搜索fiddler

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/20250304112519791.png)
