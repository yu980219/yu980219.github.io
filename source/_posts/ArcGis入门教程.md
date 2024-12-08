---
title: ArcGIS入门
tags: [GIS]
categories:
  - 其他
  - GIS
index_img: /img/arcgis.png
date: 2024/12/01 18:13:00
---

# ArcGIS入门教程

## 一、前言

ArcGis是一款地理信息系统软件，它用于创建、管理、分析和分享地图空间数据，支持地图制作，空间可视化，空间分析以及遥感处理，并且广泛应用于城市规划、环境保护、交通管理等领域。

## 二、软件下载及安装

这里以10.8举例，解压安装包

![image-20241126015107845](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260151916.png)

双击ArcGIS_Desktop_1081.exe进行常规安装

使用Crack中的AfCore.dll文件替换安装路径下的bin目录下的同名文件完成破解。

双击DesktopChinese_CN.msi，进行安装汉化。

### 软件分类

安装好之后你会获得4个客户端：ArcCatalog、ArcGlobe、ArcMap、ArcScene

平时用的最多的就是ArcMap

三维的就是ArcScene

ArcGlobe比较类似于图形地球，把所有的图层都投影到这个球上

ArcCatalog，可以管理文件夹连接，工具箱，应用程序等

在搜索栏中搜索ArcMap，打开软件。

## 三、熟悉软件操作页面

【文件】-【新建】打开的是mxt类型的文档

![image-20241126015419940](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260154006.png)

这时候我们可以创建一个空白地图，下方的gdb就是我们在安装软件的时候设置的一个数据库。

### 3.1 功能区

上方是功能区，在上方右键可以把常用的功能放出来；

左侧是内容列表，它可以展示我们放在当前这个地图文档的所有图层；

右面是目录和搜索，可以固定在右侧，可以用文件夹连接的方式连接到我们电脑的文件。

### 3.2 基本设置

点击上方【自定义】-【ArcMap选项】，把滚轮设置为惯用方式

![image-20241126024943430](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260249468.png)

点击上方【文件】-【地图文档属性】，将存储数据源的相对路径名打上对号，这个时候只要地图文档和数据源的相对路径没有改变，就不会有找不到数据源的情况了。

### 3.3 视图

中间区域是视图区域，下方可以选择数据视图和布局视图

把数据拖进来的时候，在数据视图里面可以进行编辑等操作，如果想要出图的时候，会放到布局视图里面。

1. 在页面和打印设置中，可以设置横向和纵向；

2. 设置比例尺，图片整体会放大；

3. 鼠标选中选择元素光标的话，就可以对数据框进行拖动；

4. 数据框不想变的话，选择上方平移；

## 四、ArcGIS中的文件类型

### 4.1 mxd

地图文档：存放所有图层，标注，属性，布局等

### 4.2 shapefile

![image-20241126030206904](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260302939.png)

点线面要素，它包含了很多后缀名

![image-20241126030246765](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260302805.png)

1. **shp**：存储空间数据

2. **dbf**：存放的是一些属性

3. **shx**：存储空间与属性数据关系，相当于索引

   ...除了这些还有很多，这些都是shapefile的组成

新建一个点文件，设置一下坐标系：

![image-20241126030504425](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260305452.png)

![image-20241126030626189](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260306231.png)

### 4.3 栅格（DEM）

它的后缀名包括：TIFF、esri grid、JPGE等

我们随便点开一张图片，当把图片放大到一定程度，可以发现它是由一个个方格组成的，就叫栅格，每个方格里就是一个像元，在ArcGIS里每个像元都存储了一些数据；

比如高程数据就是DEM，等等

### 4.4 数据库

**gdb和mdb**

在使用ArcGIS的时候，除了把点线面要输创建为shapefile以外，还可以自己创建一个数据库，

![image-20241126031015364](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260310393.png)

随便创建一个

![image-20241126031051000](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260310019.png)

文件地理数据库就是gdb，个人地理数据库就是mdb，他们使用起来没有太大的区别

现在在文件数据库中创建点线面要素

![image-20241126031236533](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260312579.png)

创建一个线要素

![image-20241126031316887](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260313922.png)

选择坐标系，一直点下一步即可

![image-20241126031438723](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260314746.png)

数据库和外面创建的shapefile的区别是

shapefile每一个文件都是单独个体，在数据库中相当于对每个文件进行了管理。

在移动数据库中文件的时候，只能通过这个数据库来移动

这是刚刚创建的数据库：

![image-20241126031655252](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260316287.png)

![image-20241126031711745](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411260317793.png)

我们需要拷贝给别人的时候，需要整个数据库都拷贝给别人，虽然没有那么方便，但对整个数据库都进行了统一管理，在编辑要素的时候，可以做到调整一个要素，其他所有要素都会关联的更改。

**总结：**

1. 不管是数据库要素类还是shapefile要素类，都包含了，点、线、面三要素
2. 一般存放在数据库中的栅格文件都是以esri grid保存的

## 五、坐标系

坐标系分为：地理坐标系、投影坐标系

### 5.1 地理坐标系

地球的自然表面有高山也有洼地，是崎岖不平的，我们要使用数学法则来描述他，就必须找到一个相对规则的数学面。

![image-20241126102206112](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261022155.png)

1. **大地水准面。**

大地水准面是地球表面的第一级逼近。假设当海水处于完全静止的平衡状态时，从海平面延伸到所有大陆下部，而与地球重力方向处处正交的一个连续、闭合的曲面，这就是大地水准面。重力等势面

2. **地球椭球体。**

大地水准面可以近似成一个规则成椭球体，但并不是完全规则，其形状接近一个扁率极小的椭圆绕短轴旋转所形成的规则椭球体，这个椭球体称为地球椭球体。它是地球的第二级逼近



**地心坐标系**：以总体地球为参考、以地球质心为中心的参考椭球为基准而建立的坐标系，如WGS84、CGCS2000。严格意义上，质心坐标系不存在大地原点的说法。

**参心坐标系**：在特定区域内与地球表面吻合，大地原点是参考椭球与大地水准面相切的点，如Beijing54、Xian80。

我们经常做项目使用的坐标系为2000坐标系。

| **坐标系名**   | **北京54**       | **西安80**        | **WGS84**    | **CGCS2000**                              |
| -------------- | ---------------- | ----------------- | ------------ | ----------------------------------------- |
| 参考椭球体     | Krasovsky_1940   | Xian_1980(IAG75） | WGS84        | CGCS2000                                  |
| 基准面         | D_Beijing_1954   | D_Xian_1980       | D_WGS_1984   | D_China_2000                              |
| ArcGIS中的名称 | GCS_Beijing_1954 | GCS_Xian_1980     | GCS_WGS_1984 | GCS_China_Geodetic_Coordinate_System_2000 |
| ArcGIS中的WKID | 4214             | 4610              | 4326         | 4490                                      |

**GCJ-02（火星坐标系）**：GCJ-02是由中国国家测绘局（G表示Guojia国家，C表示Cehui测绘，J表示Ju局）制订的地理信息系统的坐标系统。它其实就是对真实坐标系统进行人为的加偏处理，按照特殊的算法，将真实的坐标加密成虚假的坐标，而这个加偏并不是线性的加偏，所以各地的偏移情况都会有所不同。而加密后的坐标也常被大家称为“火星坐标系统”。WGS加密，腾讯、高德在用。————————————————

**BD09（百度自己的坐标系）**：BD09经纬度投影属于百度坐标系，它是在(GCJ02)标准经纬度的基础上进行GCJ-02加偏之后，再加上百度自身的加偏算法，也就是在标准经纬度的基础之上进行了两次加偏。。

### 5.2 投影坐标系

在地球椭球面和平面之间建立点与点之间函数关系的数学方法，称为地图投影。地球椭球表面是一种不可能展开的曲面，要把这样一个曲面表现到平面上，就会发生裂隙或褶皱。地图投影的变形通常有：**长度变形、面积变形和角度变形**。在实际应用中，根据使用地图的目的，限定某种变形。

![image-20241126102817532](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261028563.png)

**通用横轴墨卡托投影**：简称UTM，是一种国际标准化的地图投影法，使用笛卡儿坐标系，标记南纬80°至北纬84°之间的所有位置。

**高斯-克吕格投影**：又名"等角横切椭圆柱投影”。我国基本比例尺地形图除1：100万以外均采用高斯-克吕格投影

**高斯－克吕格投影分带规定**：该投影是国家基本比例尺地形图的数学基础，为控制变形，采用分带投影的方法，在比例尺1：2.5万—1：50万图上采用6°分带，对比例尺为1：1万及大于1：1万的图采用3°分带。。6°分带法：从格林威治零度经线起，每6°分为一个投影带，全球共分为60个投影带。3°分带法：从东经1°30′起，每3°为一带，将全球划分为120个投影带。

----

**动态投影**

现在打开ArcGIS，新建一个空白地图

右下角显示的是未知单位

也就是说整个地图中是没有坐标系的

![image-20241126103812561](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261038611.png)

当我们从右侧拖进第一个要素的的时候，就动态的把第一个要素的坐标系设置为了这个图层的坐标系

![image-20241126104222157](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261042211.png)

这时候地图文档的坐标系动态匹配了图层的坐标系，现在是WGS 84的坐标系

我们再向这个图层中拖入WGS 84墨卡托投影的坐标系，系统会将这个坐标系换算成WGS 84的坐标系。

----

**地图坐标系**：整个地图文档的动态投影的属性（右下角），它的坐标系就是地图坐标系

**真实坐标系**：图层在现实情况下真实所在的位置

**属性坐标系**：图层右击，属性，源，投影坐标系。



使用工具【定义投影】强行把坐标系更改之后，地理位置会发生偏移，如下

1.比如我们有一个点的经纬度是(111.753052.31.671735):

2.它在2000..GK ZONE 37下的坐标为:(37571411,3505699)

3.它在WGS 1984 UTM 49N下的坐标为:(571382.3505699)

4.如果我们通过定义投影把2000..GK ZONE 37坐标系强行更改为WGS 1984 UTM 49N，那么这个点就变成了: WGS 1984 UTM 49N下坐标为(37571411,3505699)的点

----

**工具箱**

对于没有坐标系的数据，需要定义它的投影

把shapefile中的prj文件删掉，这时候导入shapefile，ArcGIS会提示虽然有坐标，但是没有坐标系。

工具箱里找到定义投影

![image-20241126110656669](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261106711.png)

可以定义坐标系的数据。

## 六、要素编辑以及属性表的使用

### 6.1 创建要素

#### 数据导入

1. shape数据直接导入

2. csv数据：

![image-20241126111814469](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261118520.png)

右键，显示xy数据

![image-20241126111859578](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261118601.png)

x字段选x列，y字段选y列，可以使用编辑指定坐标系

![image-20241126112139373](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261121428.png)

3. CAD数据：

直接拖入CAD素材，这是一个没有空间参考坐标系的文件，它会自动被解析成5种类型的文件

Annotation：注记类型，可以转换为数据库注记

Point：点要素

Polyline：线文件

Polygon：面文件

MultiPatch：

#### 直接创建

创建一个面要素

![image-20241126113503228](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261135273.png)

![image-20241126113548379](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261135407.png)

#### 初始坐标系的选择

点击编辑

如果是地理坐标系，就是常用的WGS84和郭嘉2000

如果是投影坐标系，看一下要素的所在位置大概在哪里，比如要创建武汉的要素，武汉在地球上的经度在113、114、115范围

离中央经线越近，它的投影变形就越小。

![image-20241126114110434](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261141466.png)

选择 114E 的投影

### 6.2 属性表

**属性表记录着一个要素里面所有的子元素**

![image-20241126114455329](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261144370.png)

比如省级行政区划是个面要素，那么每个省都是一个面

上一环节，我们新建的area面，里面的属性表是空的

![image-20241126115225518](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261152558.png)

绘制一个面，点击编辑器，开始编辑，结束后停止编辑

![image-20241126120314221](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261203273.png)

再打开属性表，它就给我们创建出来了一个面

![image-20241126142602414](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261426498.png)

**属性表的每一个表头都是一个字段**

添加字段：

![image-20241126142755518](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261427559.png)

注意：编辑属性表时，一定要在要素处于编辑状态下。

**计算几何：**

在属性表中右键

如果使用数据源的坐标系，可以计算当前坐标系下：面积、周长、质心x和y的坐标等

而使用数据框的坐标系，不可以计算

**按属性选择**

![image-20241126143350915](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261433973.png)

可以按字段进行筛选，并且按筛选的条件导出，点击应用筛选，右键要素-【数据】-【导出数据】

属性表下方可以点击选中所选记录。

**字段计算器**

![image-20241126143646566](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261436605.png)

右键字段表头，可以进行计算

### 6.3 编辑要素

点击要素-【编辑器】-【编辑窗口】-【创建要素】

![image-20241126144010186](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261440223.png)

**面要素：**

在上方显示直线、弧线、追踪等

追踪：点击追踪可以移动鼠标，让程序自动描不规则的边。

编辑折点：点击折点，鼠标拖动可以对折点进行移动，同时可以添加折点和删除折点

裁剪面：可以在面的内部进行绘制，把这个面拆分多个部分，同时属性表也会变成两条。

分割工具：分割线要素

同时还可以绘制矩形、圆形、椭圆

**线要素：**

新建一个shapefile，折线要素，同样选中开始编辑，右侧创建要素面板选中折线，用构造工具画折现出来，然后双击结束。

![image-20241126144654681](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411261446740.png)

同上，依然可以在编辑器里通过两点确定一个弧线段， 也可以使用追踪。

-----

可以在点击【编辑器】使用移动

合并：按住shift多选！！！然后点击【编辑器】使用合并，属性表中两个子要素也会合并成一个子要素。

PS：平常很少用ArcGIS绘图，图一般都是CAD来出。

## 七、要素基本分析工具

准备：

导出湖北省的数据

![image-20241127012408114](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270124188.png)

按属性选择，应用，导出数据

![image-20241127012456867](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270124909.png)

![image-20241127012626716](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270126762.png)

再导入全国公路和铁路的干线

![image-20241127012729364](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270127403.png)

### 7.1 裁剪

点击上方【地理处理】或右侧直接搜索，搜索【裁剪】，比如我们想要湖北省和铁路干线相交的部分。

输入要素：被裁减的要素（线图层 transport）

裁剪要素：想要的范围（湖北省）

![image-20241127013012919](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270130953.png)

裁剪结果：

![image-20241127013113043](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270131085.png)

### 7.2 缓冲区

想做一个主干线1公里的影响范围

【缓冲区】

![image-20241127013852248](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270138287.png)

如果生成的不对，可能是图层坐标系不对，可以使用【定义投影】功能更改坐标系

这个就是生成出的1千米的缓冲区

![image-20241127013944509](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270139549.png)

这里可以用测量去量一下

![image-20241127014030058](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270140086.png)

![image-20241127014058731](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270140766.png)

### 7.3 多环缓冲区

有时想对道路的影响进行分层，要怎么做

比如距离道路200米可能影响最大，距离道路1千米或者2千米影响变小

在右侧搜索【多环缓冲区】，和生成单个缓冲区基本类似，可以根据不同距离生成不同缓冲区。

![image-20241127014815263](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270148300.png)

生成结果就不是一个单个缓冲区，就是不同距离的缓冲区。

![image-20241127014837892](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270148930.png)

### 7.4 融合

新建一张空白地图，导入一张面要素。

![image-20241127015203669](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270152725.png)

这张面要素，有47个子要素，但是只有10种土地类型，所以有一些面可能不是在一起的。

现在要把它变成每个类别只有一个要素，就可以用到【融合】工具

融合：可以对某个要素，根据某个字段进行融合

根据融合字段，把要素合并为同一个子要素。

![image-20241127015449281](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270154321.png)

创建多部件要素：不勾选，如果两个子要素不相连，就不会合并成同一个子要素。

融合结果：

![image-20241127015828092](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270158152.png)

### 7.5 合并

现在左侧有7个要素，现在要把它们合并在一起

![image-20241127020031676](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270200717.png)

#### 7.5.1 合并

![image-20241127020338814](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270203859.png)

属性表中，相同的字段放在了一起，独有的字段也会展示，相当于并集。

![image-20241127020410215](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270204273.png)

#### 7.5.2 联合

![image-20241127020634116](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270206154.png)

属性表中，共有的字段也会区分开来，如果不是本要素的，就取-1

![image-20241127020712106](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270207157.png)

----

### 7.6 相交

新建一个空白图层

![image-20241127021047581](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270210615.png)

像这种有一部分漏在外面的，这种两个要素，其实可以用裁剪。

这时候演示一下相交。

![image-20241127021258112](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270212157.png)

生成结果

![image-20241127021320618](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270213656.png)

### 7.7 点线面互转

想把一个面要素转换成线要素，搜索【要素转线】

![image-20241127021640041](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270216081.png)

生成结果

![image-20241127021658765](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270216818.png)

---

如果使用【要素转面】线必须是闭合的

----

要素转点

### 7.8 密度分析

新建一个空白地图

搜索【密度】，会出现点密度分析、线密度分析、核密度分析

#### 7.8.1 点密度分析

这时候生成的是一个栅格，就不再是要素了。

第一次使用可能会报错，改一下投影，point也要改下投影，输出到文件夹中

![image-20241127022759042](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270227081.png)

![image-20241127023124234](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270231279.png)

结果

![image-20241127023150578](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270232233.png)

这个长条不是我们想要的范围，

因为在分析的时候没有设置栅格分析的区域是多大。

点击【地理处理】-【环境】-【栅格分析】

![image-20241127023454509](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270234545.png)

重新生成点密度分析

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270236198.png)

再次查看结果

![image-20241127023759447](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270237485.png)

#### 7.8.2 线密度分析

把road拖进来

![image-20241127024113341](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270241382.png)

比如一个圆型的邻域，里面线有多长，单位是米\每平方千米

#### 7.8.3 核密度分析

### 7.9 属性连接

可以将要素的属性表和外部xls表格根据相同的列进行连接

![image-20241127024823078](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270248132.png)

![image-20241127025004306](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270250344.png)

现在进行核密度分析，对每一个地级市的点进行人口密度分析

![image-20241127025237577](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270252620.png)

生成结果

![image-20241127025303190](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411270253229.png)

可以看到右侧人口密度比较大，人口数比较多。

## 八、矢量数据可视化

拖入这两个文件

![image-20241201172640868](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011726951.png)

链接人口数据，打开city属性表，选【连接和关联】-【连接】

![image-20241201172824280](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011728318.png)

人口数量就已经被链接进来了

![image-20241201172922194](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011729241.png)

添加一个字段，计算人口密度，选择【添加字段】

![image-20241201173024752](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011730781.png)

以同样的方式添加【面积】字段area

面积字段在列上右键，点击【计算几何】计算面积，单位选择平方千米

在人口字段，使用字段计算器，计算人口密度

使用人口数量/面积

### 8.1 标注

在HB_city上右键，属性，标注，打勾【标注此图层的要素】，标注的字段修改为【市】

![image-20241201173533591](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011735634.png)

标注可以用表达式，表达式中可以用VB，python，JavaScript脚本

### 8.2 符号系统

对图层右键，属性，选择符号系统

#### 8.2.1 单一符号

把每一个要素用统一的符号进行表达，会把这个文件内所有的要素设置为统一的要素。

#### 8.2.2 类别

值字段选择【市】，点击【添加所有值】，添加所有的市，选择色带。

![image-20241201174710438](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011747493.png)

值字段还可以用人口密度

多个字段：可以按多个字段来进行显示

#### 8.2.3 数量

这时候选的字段必须是数值类型的字段，选择【密度】字段，分类可以选择分4类

![image-20241201175001384](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011750436.png)

可以看到每个类别的范围，【归一化】可以选择百分比

- 分级符号：用符号的大小来表示，形状越大，表示这个值也越大
- 比例符号：有一个最大值和最小值的符号，其他数据按照比例来进行缩放和放大
- 点密度：根据值大小计算出的值密度，密度越高，说明这个字段值越大。

#### 8.2.4 图表

为每个要素绘制【饼图】，制作的是选中的字段，在这个要素中的一个饼图

![image-20241201175537737](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011755785.png)

其余的图形是一样的。

## 九、栅格文件

### 什么是栅格？

最简形式的栅格由按行和列（或格网）组织的像元（或像素）矩阵组成，其中的每个像元都包含一个信息值（例如温度）。栅格可以是数字航空像片、卫星影像、数字图片或甚至扫描的地图。

![image-20241201180231941](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011802970.png)

- 专题数据（也称为离散数据）表示土地利用或土壤数据等要素。
- 连续数据表示温度、高程或光谱数据（例如，卫星影像或航空像片）等现象。
- 图片则包括扫描的地图或绘图，以及建筑物照片。

### 栅格的分类

![image-20241201180334834](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011803894.png)

----

### 什么是像元？

- 像元，亦称像素点或像元点。即影像单元（picture element）。是组成数字化影像的最小单元。像元的大小决定着栅格中图案或要素呈现的粗细程度。
- 像元的尺寸可大可小，具体可根据栅格数据集所描述的表面，以及表面中要素的表达需要来确定。它可以是平方千米、平方英尺，甚至是平方厘米。

![image-20241201180735485](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202412011807525.png)

- 像元大小越小，则栅格将越平滑或越详细。但是像元数量越多，所需的处理时间会越长，占据的存储空间也越大。
- 如果像元大小过大，则可能会出现信息丢失或精细的图样变得模糊的情况。

### 栅格的优缺点

优点：

- 数据结构更加简单，即由像元组成矩阵结构，其中的像元值表示坐标，有时与属性表相关联
- 格式更加强大，可进行高级的空间和统计分析
- 可以表示连续表面以及执行表面分析
- 点、线、面和表面都可同样存储
- 对复杂数据集也可执行快速叠置

缺点：

- 由于栅格数据集的像元尺寸具有局限性，所以可能会带来空间误差。
- 栅格数据集可能会非常大。虽然分辨率会随着栅格像元大小的减小而提高，但这会占用更多的磁盘空间，而且会拖慢处理速度。对于给定区域，将栅格像元大小更改为现有大小的一半时，所需的存储空间会增大为原来的四倍，具体情况取决于所使用的数据类型和存储技术。
- 将数据重建到固定间距的栅格像元边界时也会损失一定的精度。

## 内容来源

[ArcGIS基础教程](https://www.bilibili.com/video/BV1Ge4y127UA/)