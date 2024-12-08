---
title: GeoServer基础教程
tags: [geoserver,Java]
categories:
  - 其他
  - GIS
index_img: /img/geoserver.png
date: 2024/12/09
---



# GeoServer基础教程

## 一、GeoServer介绍及安装

GeoServer是一个用Java编写的开源软件服务器，允许用户共享和编辑地理空间数据。它为互操作性而设计，使用开放标准发布来自任何主要空问数据源的数据。

官网：https://geoserver.org/

### 介绍

作为一个社区驱动的项目，GeoServer由来自世界各地的不同个人和组织开发、测试和支持。

GeoServer实现了行业标准的OGC协议，如Web要素服务(WFS)、Web地图服务(WMS)和Web覆盖服务(WCS)。其他格式和发布选项作为扩展提供，包括Web处理服务(WPS)和Web地图瓦片服务(WMTS)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412032344487.png)

GeoServer作为一款优秀的开源GIS服务器，在开源方案越来越流行的今天成为企事业单位从事GIS相关项目活动必须考虑的一个选择。

相较于商业GIS软件如ArcGIS Server、SuperMap iServer等地图服务器软件，GeoServer能实现绝大多数场景下项目对GIS功能的要求，而且有活跃的社区支持，在实际生产中也越来越受到决策者和开发者的青睐。

最重要的可能是它的开源性质了，任何个人和单位都可以免费下载使用它。这恐怕是他相较于商业软件受欢迎的重要因素了。作为GIS开发人员，能够使用GeoServer进行开源GIS项目的研发，也成为一名合格GIS从业者的基本要求。GeoServer作为一款优秀的开源GIS服务器软件，也成为GIS初学者了解开源GIS方案最佳的实践方案。

### GeoServer的诞生历史

GeoServer于2001年由总部位于纽约的非盈利技术孵化器open planning project(TOPP)启动。TOPP正在创建一套工具，以实现开放民主，并帮助政府更加透明。第一个是GeoServer，这是因为人们认识到，共享空问数据的能力将(成为)大大增强公民参与政府和城市规划的一套。

GeoServer的创始人设想了一个类似于万维网的地理空间网。通过万维网，人们可以搜索和下载文本。通过地理空问网，人们可以搜索和下载空问数据。数据提供者将能够直接将他们的数据发布到这个网站上，用户可以直接访问它，而不是现在存在的问接和繁琐的数据共享方法。

GeoServer的相关人员创建了GeoTools项目，这是一个开源的GIS Java工具包。通过GeoTools，增加了对shapefile、Oracle数据库、ArcSDE集成等的支持。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412032347381.png)

大约在GeoServer成立的同时，OpenGIS联合会(现在的open geospatial联合会)正在开发Web要素服务标准。它使用GML(地理标记语言)，一种互操作的数据格式，指定了一种协议，使空问数据直接在web上可用。还创建了一个Web地图服务，一个用于创建和显示从空问数据创建的地图图像的协议。

其他项目变得相互关联。Refractions Research创建了一个免费开放的空问数据库PostGIS，使GeoServer能够连接到一个免费的数据库。另外，MetaCarta最初创建了OpenLayers,一个基于开源浏览器的地图查看工具。这些工具一起增强了GeoServer的功能。

这使得使用开源技术路线构建GIS工作流成为可能，数据可以从PostGIS流入GeoServer、再应用OpenLayers将GeoServer发布的地图服务在前端进行展现和互操作。

GeoServer现在可以从十几个空间数据源读取数据，并以多种不同的格式输出。现在已经是第二个十年了，GeoServer正在继续它的使命，让所有人都能更容易地访问空问数据。

### Geoserver在不同平台上的安装

GeoServer可以安装在不同的操作系统平台上:

- Windows
- Mac OS
- Linux

同时，GeoServer也有两种安装方式：

- 二进制文件安装

- web 存档文件安装(webarchive)即war包安装。

需要注意的是，不同于桌面软件.exe形式的安装，GeoServer只需要下载后在安装目录中执行/bin/startup.bat或/bin/startup.sh命令即可。

### 安装前准备

在讲下一节GeoServer两种安装方式之前，请确保你的操作系统已经安装了JAVA环境:

确保您的系统上安装了Java运行时环境(JRE)。GeoServer需要java8或java11环境，正如open jdk windows安装程序所提供的那样。

选择版本下载：https://sourceforge.net/projects/geoserver/files/GeoServer/

- **tomcat11**必须保证**jdk11**，否则启动会报错
- 如果我们使用Java8环境，那么GeoServer的适配版本在2.9.x到2.22.x之间。关于这一点一定要注意，理论上总是说可以向下兼容，但进行实践的时候似乎总是很难做到。
- 亲测：geoserver选择**2.22.5**，tomcat使用**8.0.50**，可以使用**jdk8**，其他版本未知

1. 二进制文件下载:(与平台无关，下载即可)

http://geoserver.org/release/stable/

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412032351569.png)

进入安装目录，在bin目录下找到startup.bat，启动GeoServer程序。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412040054745.png)

访问：http://localhost:8080/geoserver

2. web 存档文件(war包)下载:(与平台无关，下载即可)

http://geoserver.org/release/stable/

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412032357322.png)

解压后将war包放在tomcat安装目录webapps下，重新启动tomcat。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412040016947.png)

访问：http://localhost:8080/geoserver

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412040052938.png)

----

GeoServer在不同的操作系统上安装并运行我们可以理解，下面我们主要讨论GeoServer的两种安装方式:

二进制文件安装在Windows上安装GeoServer的一种方法是使用与平台无关的二进制文件。这个版本是捆绑在Jetty中的GeoServer web应用程序，Jetty是一个轻量级和可移植的应用服务器。它具有在所有操作系统中工作非常相似的优点，并且设置起来非常简单。(可以将jetty理解为tomcat，此处即为内嵌了GeoServerweb应用程序的tomcat)

web 存档文件安装(web archive)即war包安装。GeoServer应用程序被打包为独立servlet，用于现有应用程序服务器，如ApacheTomcat和Jetty。这里是将GeoServer作为现有servlet容器(如Tomcat)的一部分运行。

另外一种常用的部署是将GeoServer部署在Linux平台上，这也是我平时常用的部署方式其方式方法和在Windows上部署类似，不同之处是你需要部署Liunx版本(tar.gz)的tomcat,且执行的命令是 /sh startup.sh

## 二、GeoServer Web管理界面

### GeoServer首次登录

GeoServer有一个基于浏览器的web管理界面应用程序，用于配置GeoServer的所有方面，从添加和发布数据到更改服务设置。

- 可通过web浏览器访问web管理界面

网址为:http://<host>:<port>/geoserver

- 如果是默认安装

则访问地址为:http://localhost:8080/geoserver

### 登录

要更改任何服务器设置或配置数据，必须首先对用户进行身份验证。导航到web界面的右上角以登录到GeoServer。默认管理凭据为:

User name: admin

Password: geoserver

很多新手或者长时间不用GeoServer的人会不清楚或忘记默认用户名和密码，一定要牢记。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412040935910.png)

## 三、使用GeoServer发布shapefile文件

实验数据下载地址：
https://docs.geoserver.org/stable/en/user/_downloads/30e405b790e068c43354367cb08e71bc/nyc_roads.zip

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041023777.png)

shapefile实际上是文件的集合(扩展名:.shp、.dbf、.shx、.pj，有时还有其他文件)。所有这些文件都需要存在于同一目录中，以便GeoServer能够准确读取它们。

在使用GeoServer时，强烈建议使用.prj文件(虽然不是必需的)，因为它包含有价值的投影信息。如果没有它，GeoServer可能无法加载shapefile。

1. 创建一个工作区

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041026098.png)

2. 创建新的数据存储

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041028164.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041031419.png)

3. 新建一个图层

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041036827.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041040604.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041041378.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041042802.png)

4. 查看图层

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041043168.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041045239.png)

## 四、使用GeoServer发布GeoTIFF影像

### 实验数据

TIFF (Tag Image File Format)图像文件是图形图像处理中常用的格式之一,其图像格式很复杂,但由于它对图像信息的存放灵活多变,可以支持很多色彩系统,而且独立于操作系统.

因此得到了广泛应用。在各种地理信息系统、摄影测量与遥感等应用中,要求图像具有地理编码信息,例如图像所在的坐标系、比例尺、图像上点的坐标、经纬度、长度单位及角度单位等等。对于存储和读取这些信息,纯TEF 格式的图像文件是很难做到的,而GeOTIFF 作为TIFF 的一种扩展,在TIFF 的基础上定义了一些GeoTag(地理标签),来对各种坐标系统、椭球基准、投影信息等进行定义和存储,使图像数据和地理数据存储在同一图像文件中,这样就为广大用户制作和使用带有地理信息的图像提供了方便的途径。

----

步骤同上

1. 添加新的数据存储-GeoTIFF
2. 发布图层

## 五、安装postgreSQL和postgis

> 安装时，须保证pgsql和postgis版本对应，我这里选择的是
>
> pgsql：13.18.1
>
> postgis：pg13x64-3.5.0-1
>
> 而且postgis必须安装在postgreSQL文件夹下，不能安装在pgsql文件夹下的任何子文件夹内，否则会报错。
>
> 如果pgsql的版本大于等于15，navicat是打不开的。

**安装postgreSQL**：https://www.postgresql.org/

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041515423.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041517235.png)

测试服务是否已经启动

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041526193.png)

随着pgSQL安装会自动安装一个manager管理软件

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041527947.png)

本地的pgSQL

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041529598.png)

-----

**安装postgis插件**

https://download.osgeo.org/postgis/windows/

勾选创建一个空间数据库

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041652394.png)

postgis库和普通的库区别就在于，在扩展里，多了postgis的扩展

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412041739152.png)

## 六、使用GeoServer发布Postgis数据表

### 1.创建postgis数据库

打开pgAdmin，先进行汉化

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061624052.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061625432.png)

----

**在前几章已经创建了postgis数据库，那如何新建一个postgis数据库？**

1. 模板方式

在数据库上右键 - 创建 -数据库

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061627624.png)

选择之间创建好的postgis模板的数据库，使用它的模板

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061633534.png)

2. 使用命令

在创建好的空白数据库上 -  右键 - 查询工具

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061634838.png)

使用下方命令，点击执行

```sql
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
CREATE EXTENSION postgis_sfcgal;
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION address_standardizer;
CREATE EXTENSION address_standardizer_data_us;
CREATE EXTENSION postgis_tiger_geocoder;
```

即可在扩展中看到

### 2.下载实验数据

> 下载地址：https://docs.geoserver.org/stable/en/user/%20downloads/bbd4b941ceb36074c9d19feab3f57609/nyc%20b%20uildings.zip

导入数据到pgsql中

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061644427.png)

### 3.启动geoserver

启动geoserver，使用test工作区

点击【存储仓库】，添加新的数据存储，选择postgis

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061649224.png)

填写pg数据库的连接参数，其他保持默认：

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061654723.png)

选择nyc_buildings，点击发布

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061653058.png)

其他保持默认，但需要计算一下边框

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061655054.png)

点击保存，发布图层

点击图层预览，搜索nyc，可以预览

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061658353.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412061658461.png)

## 七、使用openLayers调用geoserver发布的wms服务

> openlayers官网：https://openlayers.org/

点击QuickStart，复制代码下来

现在官网已经使用npm方式，没有html静态代码了

注意：

1. 实例中本来是用cdn，但现在貌似访问不到了
2. 请求地图png需要科学上网
3. shape样例用的北京市中心的shape，下载网址：https://gitcode.com/open-source-toolkit/d07f9/blob/main/geoserver%E7%BB%83%E4%B9%A0%E6%95%B0%E6%8D%AE.rar，下载好后发布图层

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <!--注意：openlayers 原版的比较慢，这里引起自己服务器版-->
    <link rel="stylesheet" href="http://openlayers.vip/examples/css/ol.css" type="text/css">
    <style>
       /* 注意：这里必须给高度，否则地图初始化之后不显示；一般是计算得到高度，然后才初始化地图 */
      .map {
        height: 400px;
        width: 100%;
      }
    </style>
    <!--注意：openlayers 原版的比较慢，这里引起自己服务器版-->
    <script src="http://openlayers.vip/examples/resources/ol.js"></script>
    <title>OpenLayers example</title>
  </head>
  <body>
    <h2>My Map</h2>
    <!--地图容器，需要指定 id -->
    <div id="map" class="map"></div>
    <script type="text/javascript">
      var map = new ol.Map({
        // 地图容器
        target: 'map',
        // 地图图层，比如底图、矢量图等
        layers: [
          new ol.layer.Tile({
            // 谷歌底图
            source: new ol.source.OSM()
          })
        ],
        // 地图视野
        view: new ol.View({
          // 定位
          center: ol.proj.fromLonLat([37.41, 8.82]),
          // 缩放
          zoom: 4
        })
      });
    </script>
  </body>
</html>
```

新建一个html，把上述代码粘贴到里面

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412070331342.png)

在上面的基础上，再添加geoserver WMS资源，添加到ol.Map中的layers下和 ol.layer.Tile同级

```javascript
new ol.layer.Image({
          source: new ol.source.ImageWMS({
            ratio: 1,
            url: "http://localhost:8080/geoserver/test/wms",
            crossOrigin: 'anonymous', //跨域声明
            params: {
              'FORMAT': 'image/png',
              "VERSION": '1.1.1',
              "exceptions": 'application/vnd.oge.se_inimage',
              "LAYERS": 'test:community_price'
            }
          })
        })
```

url：地图服务的url地址，在F12的请求url中找

params：在请求参数中都能找到

接下来找定位：

在图层预览这里鼠标移动到正中心，查看它的经纬度坐标，填入

**ol.proj.fromLonLat**方法中

**解决tomcat跨域问题：**

tomcat安装目录下 conf - web.xml，589左右

添加下面代码，重启tomcat：

```xml
<filter>
<filter-name>CorsFilter</filter-name>
<filter-class>org.apache.catalina.filters.CorsFilter</filter-class>
<init-param>
<param-name>cors.allowed.origins</param-name>
<param-value>*</param-value>
</init-param>
<init-param>
<param-name>cors.allowed.methods</param-name>
<param-value>GET,POST,HEAD,OPTIONS,PUT</param-value>
</init-param>
<init-param>
<param-name>cors.allowed.headers</param-name>
<param-value>*</param-value>
</init-param>
<init-param>
<param-name>cors.exposed.headers</param-name>
<param-value>Access-Control-Allow-Origin,Access-Control-Allow-Credentials</param-value>
</init-param>
<init-param>
<param-name>cors.support.credentials</param-name>
<param-value>true</param-value>
</init-param>

</filter>
<filter-mapping>
<filter-name>CorsFilter</filter-name>
<url-pattern>/*</url-pattern>
</filter-mapping>
```

全部配置好后，打开网页

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412070444909.png)

## 八、使用geoserver生产wmts瓦片

确定要生成瓦片这两个选项打勾

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082314716.png)

生成过的图层在下方都会生成瓦片服务

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082320791.png)

点击按钮生成瓦片，（后面的是清空瓦片的意思）

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082321253.png)

create new task就是生成瓦片的任务，它把遥感影响切分成小块任务，当你在浏览器访问时，访问对应的瓦片回来，不用浏览器实时用wms去渲染，提高它的访问速度，用的是空间和时间的概念。

1. Type of operation: 可以选择，生成全部，生成缺失的瓦片，移除瓦片。
2. Grid Set:生成瓦片时，用什么坐标系，用什么策略。
3. Zoom start + Zoom stop: 起始缩放级别和最终缩放级别
4. Modifiable Parameters: 默认即可
5. Bounding box: 指定切片范围

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082322451.png)

提交之后会给出切片的进程信息，如果再次刷新没有了，就是进程结束了。

![image-20241208232641388](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082326421.png)

----

geoserver生成的瓦片都是默认在内存中

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082332806.png)

如何让他生成在磁盘上？官网介绍，需要配置web.xml并重启项目

> 官方文档：https://docs.geoserver.org/latest/en/user/geowebcache/config.html#changing-the-cache-directory

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082332071.png)

先清空掉内存中的瓦片，会提示从磁盘中清空0B的瓦片，也说明瓦片根本没有生成在磁盘上

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082337243.png)

停止geoserver，在tomcat中的geoserver找到web.xml

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082334060.png)

配置瓦片输出地址

```xml
 <context-param>
   <param-name>GEOWEBCACHE_CACHE_DIR</param-name>
   <param-value>C:\Users\yu\Desktop\cache</param-value>
</context-param>
```

再次启动geoserver，重新生成瓦片

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082338957.png)

查看对应文件夹，已经生成到了对应的文件夹下

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082341565.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082341215.png)

----

在网络集下可以以某个为模板，创建新的瓦片策略。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082345485.png)

制作好后，要在图层 - Tile Caching 中增加瓦片策略

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082350404.png)

## 九、WMTS瓦片的迁移使用

核心思路：

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412082352907.png)

上节课说，如果服务特别大，瓦片特别多，一个节点可能生成不过来，这时候就用其他几个子节点来帮忙。

比如一共有15个瓦片，让节点1生成1-10，节点2生成11-13，节点3生成14-15

1. **在目标GIS服务器上发布wms服务，不生产瓦片。(或者发布空服务，只是为了构建bbox)**

2. **在其他GeoServer服务器上创建wms服务，生产wmts瓦片。**
3. **将所有其他GeoServer服务器上生产的wmts瓦片复制到目标GeoServer服务器对应服务瓦片目录下。**

建立四个tomcat服务，server是负责对外提供服务，node负责生成瓦片

四个tomcat服务，需要更改tomcat端口以及其他端口，geoserver中需要更改缓存生成的位置，依次启动服务

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412090009844.png)

在node1中生成0-10层的瓦片，node2生成11-13，node3生成14-15

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412090009460.png)

为什么要生成空服务server，就是为了让它自动确定边界，边框正确，容器范围足够大，才能正常展示。

复制其他节点cache文件夹下的所有瓦片，到server的cache文件夹下的瓦片目录下。

----

转自：[GeoServer中文版软件使用教程](https://www.bilibili.com/video/BV1co4y1C7NJ/)