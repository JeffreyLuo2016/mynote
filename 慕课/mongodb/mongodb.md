#mongodb入门
<!-- MarkdownTOC -->

- [基本知识](#基本知识)
	- [创建mongodb数据库服务](#创建mongodb数据库服务)
	- [连接mongodb服务器](#连接mongodb服务器)
	- [mongoDB的基本操作之数据写入和查询](#mongodb的基本操作之数据写入和查询)
	- [mongoDB的基本操作之数据更新](#mongodb的基本操作之数据更新)
	- [mongoDB的基本操作之更新不存在的数据](#mongodb的基本操作之更新不存在的数据)
	- [mongoDB的基本操作之更新多条数据](#mongodb的基本操作之更新多条数据)
	- [mongoDB的基本操作之删除数据](#mongodb的基本操作之删除数据)
	- [mongoDB的基本操作之创建索引](#mongodb的基本操作之创建索引)
- [索引简介](#索引简介)
	- [索引的种类与使用](#索引的种类与使用)
		- [_id索引](#_id索引)
		- [单键索引](#单键索引)
		- [多键索引](#多键索引)
		- [复合索引](#复合索引)
		- [过期索引](#过期索引)
	- [索引的匹配规则](#索引的匹配规则)
	- [如何建立合适的索引](#如何建立合适的索引)
	- [索引建立的情况评估](#索引建立的情况评估)
- [全文索引](#全文索引)
	- [建立方法](#建立方法)
	- [使用全文索引进行查询](#使用全文索引进行查询)
	- [使用全文索引相似度查询](#使用全文索引相似度查询)
	- [全文索引的使用限制](#全文索引的使用限制)
- [索引属性](#索引属性)
- [地理位置索引](#地理位置索引)
	- [2d索引](#2d索引)
	- [2dsphere索引](#2dsphere索引)
	- [索引构建情况分析](#索引构建情况分析)
		- [mongostat](#mongostat)
		- [profile集合](#profile集合)
		- [日志](#日志)
		- [explain\(\)](#explain)
- [MongoDB安全楔子](#mongodb安全楔子)
	- [MongoDB安全概览](#mongodb安全概览)
	- [开启权限认证](#开启权限认证)
	- [MongoDB 创建用户](#mongodb-创建用户)
	- [MongoDB 用户角色](#mongodb-用户角色)

<!-- /MarkdownTOC -->
## 基本知识
### 创建mongodb数据库服务

```sh
mkdir mongodb_simple
cd mongodb_simple
mkdir data
mkdir log
mkdir conf
mkdir bin
cp ../../software/mongodb-osx-x86_64-3.2.6/bin/mongod bin/
cd conf
vim mongod.conf
		#输入以下配置信息
		port = 12345
		dbpath = data
		logpath = log/mongod.log
		fork = true
./bin/mongod -f conf/mongod.conf #启动mongodb数据库

```

### 连接mongodb服务器
```sh
cp ../../software/mongodb-osx-x86_64-3.2.6/bin/mongo bin
./bin/mongo --help #查看使用说明
./bin/mongo 127.0.0.1:12345/test #连接到数据库服务
db.shutdownServer() #关闭mongdb服务
```

### mongoDB的基本操作之数据写入和查询
```js
show dbs //显示数据库
use test //切换到test数据库,不存在数据库会自动创建
db.dropDatabase() //删除数据库

db.imooc_collection.insert({x:1}) //创建imooc_collection集合，并插入数据。
db.imooc_collection.find({x:1}) //查询条件为{x:1}的记录，find条件可以为空，默认情况下返回所有记录。
db.imooc_collection.insert({x:2,_id:1}) //_id全局唯一，可自己指定，不指定会自动生成。

for(i=3;i<100;i++)db.imooc_collection.insert({x:i})  //使用js语法插入多条语句

db.imooc_collection.find().count() //查询记录总数
db.imooc_collection.find().skip(6).limit(5).sort({x:1}) //查询，跳过前6条，只查6条，按x升序排序, 传入{x:-1}则倒序排序.

```

### mongoDB的基本操作之数据更新
```js
db.imooc_collection.update({x:1},{x:99}) //修改{x:1}的记录为{x:9}
db.imooc_collection.update({z:100},{$set:{y:99}}) //更新{z:100}的记录中的y为99,使用$set表示部分修改，存在的记录会被修改，不存在的记录保持原样，不适用set则所有记录都会被{y:99}覆盖。
```

### mongoDB的基本操作之更新不存在的数据
```js
db.imooc_collection.update({y:999},{y:9999},true) //第三个参数为true表示将要更新的数据如果不存在，就创建一条新的记录。
```

### mongoDB的基本操作之更新多条数据
```js
db.imooc_collection.update({c:1},{$set:{c:2}},false,true) //第四个参数表示是否多条更新，默认只更新查找到的第一条数据。设为true标识更新所有查询到的数据。
```

### mongoDB的基本操作之删除数据
```js
db.imooc_collection.remove({x:2}) //删除{x:2}的记录，为了数据安全，默认不允许不传参数。
show tables //显示当前的库中的表
db.imooc_collection.drop() //直接删除表
```

### mongoDB的基本操作之创建索引
```js
db.imooc_collection.getIndexes() //查询集合中的索引
db.imooc_collection.ensureIndex({x:1}) //创建索引, key的值为1代表正向拍训， key的值为-1代表逆向排序。
```



## 索引简介

### 索引的种类与使用

#### _id索引
- _id索引是绝大多数集合默认建立的索引。
- 对于每条插入的数据，MongoDB都会自动生成一条唯一的_id字段。

#### 单键索引
1. 单键索引是最普通的索引	
	例如一条记录：{x:1,y:1,z:1}, 可以给x创建索引，加速用x作为条件的查询。
2. 与_id索引不同， 单键索引不会自动创建

#### 多键索引
- 多键索引与单键索引创建形式相同，区别在于字段的值。
	- 单键索引：值为一个单一的值，例如字符串，数字或者日期。
	- 多键索引：值具有多个记录，例如一个数组。
	```js
	db.imooc_2.insert({x:[1,2,3,4,5]})//对于这条语句来讲，mongodb便为集合创建了多键索引.
	```

#### 复合索引
- 当我们的查询条件不只有一个时，就需要建立复合索引。
```js
db.imooc_2.ensureIndex({x:1,y:1}) //这条语句创建复合索引，加速使用{x:1,y:1}作为条件的查询。

//例如：
db.imooc_2.find({x:1,y:2}) //会使用前面创建的索引查询。

```

#### 过期索引
1. 过期索引：是在一段时间后会过期的索引。
2. 在索引过期后，相应的数据会被删除。
3. 这适合存储一些在一段时间之后会失效的数据，比如用户的登录信息，存储的日志等。	

示例：	
```js
db.imooc_2.ensureIndex({time:1},{expireAfterSeconds:30}) //创建会再30秒后过期的索引
db.imooc_2.insert({time:new Date()}) //插入一条数据 此数据将会在30秒后自动被删除。
```

- 过期索引的限制 
	1. 存储在过期索引字段的值必须是指定的时间类型。(说明：必须是ISODate或者是ISODate数组，不能使用时间戳，否则不能被自动删除。)	
	2. 如果指定了ISODate数组，则按照最小时间进行删除。
	3. 过期索引不能是复合索引。
	4. 删除时间不是精确的。		

		> 说明： 删除过程是由后台程序每60s跑一次，而且删除也需要一些时间，所以存在误差。
		


### 索引的匹配规则

### 如何建立合适的索引

### 索引建立的情况评估



## 全文索引

全文索引：对字符串与字符串数组创建全文可搜索的索引		
适用情况：{author:"", titile:"", article:""} 		

### 建立方法
```js
db.articles.ensureIndex({key:"text"})
db.articles.ensureIndex({key_1:"text",key_2:"text"})
db.articles.ensureIndex({"$**":"text"})

//demo
db.imooc_2.ensureIndex({"article":"text"})
```

### 使用全文索引进行查询
```js
//插入三条数据
db.imooc_2.insert({"article":"aa bb cc dd ee"})
db.imooc_2.insert({"article":"aa bb rr gg"})
db.imooc_2.insert({"article":"aa bb cc hh di di hahaha"})


//查询  在mongodb中一个数据集合只能创建一个全文索引
db.articles.find({$text:{$search:"coffee"}})
db.articles.find({$text:{$search:"aa bb cc"}}) //查询包含aa或bb或cc的记录
db.articles.find({$text:{$search:"aa bb -cc"}}) //包涵aa，bb 但是不包含cc的记录
db.articles.find({$text:{$search:"\"aa\" \"bb\"" \"cc\""}}) //查询既包含aa 又包含bb 又包含cc的记录
```


### 使用全文索引相似度查询

全文索引相似度:	
	`$meta操作符：{score:{$meta:"textScore"}}` 		
	写在查询条件后面可以返回返回结果的相似度.	
	与sort一起使用，可以达到很好的实用效果。		

demo:	
```js
db.imooc_2.find({$text:{$search:"aa bb"}},{score:{$meta:"textScore"}})

//上面命令输出如下结果：
{ "_id" : ObjectId("57482b6c5cdd9b3192fbad32"), "article" : "aa bb cc hh di di hahaha", "score" : 1.1428571428571428 }
{ "_id" : ObjectId("57482b575cdd9b3192fbad30"), "article" : "aa bb cc dd ee", "score" : 1.2 }
{ "_id" : ObjectId("57482b5e5cdd9b3192fbad31"), "article" : "aa bb rr gg", "score" : 1.25 }
{ "_id" : ObjectId("57482eb25cdd9b3192fbad33"), "article" : "aa bb", "score" : 1.5 }

//对上面的结果按相似度进行排序
db.imooc_2.find({$text:{$search:"aa bb"}},{score:{$meta:"textScore"}}).sort({score:{$meta:"textScore"}})
```

### 全文索引的使用限制
全文索引虽然非常强大，但是同样存在着限制：			
1. 每次查询，只能指定一个$text查询；		
2. `$text查询不能出现在$nor的查询中；`		
3. 查询中如果包含了$text, hint将不在起作用；		
4. 很可惜，mongodb的全文索引还不支持中文。		


## 索引属性
创建索引是的格式：		
`db.collection.ensureIndex({param},{param})`		
其中第二个属性便是索引的属性。		

比较重要的属性有：		
1. 名字`db.imooc_2.ensureIndex({},{name:" "})` 		
2. 唯一性`db.imooc_2.ensureIndex({},{unique:true/false})` 		
3. 稀疏性`db.imooc_2.ensureIndex({},{sparse:true/false})` 	
4. 是否定时删除`db.imooc_2.ensureIndex({},{name:" "})` 	

name:		
```js 
db.imooc_2.ensureIndex({x:1,y:1,z:1,m:1},{name:"normal_index"}) //创建名称为`normal_index`的索引
db.imooc_2.dropIndex("normal_index") //以名字为参数删除索引
```

unique:		
```js
db.imooc_2.ensureIndex({m:1,n:1},{unique:true}) //创建唯一性索引
```

sparse和expireAfterSeconds:		
```js
db.imooc_2.find({m:{$exists:true}}) //查询存在m字段的数据
db.imooc_2.ensureIndex({m:1},{sparse:true}) //创建稀疏性索引

db.imooc_2.find({m:{$exists:false}}).hint("m_1") //强制使用索引`m_1`
```

过期索引:		
TTL,参考之前的记录。		

## 地理位置索引 	

- 概念：将一些点的位置存储在MongoDB中，创建索引后，可以按照位置来查找其他点。
- 子分类：
	1. 2d索引， 用于存储和查找平面上的点。
	2. 2dsphere索引，用于存储和查找球面上的点。
- 查找方式：
	1. 查找距离某个点一定距离内的点；
	2. 查找包含在某区域内的点；

### 2d索引
- 创建方式：
```js
db.collection.ensureIndex({"w":"2d"})
```

- 查询方式：
	1. $near查询：查询距离某个点最近的点；
	2. $geoWithin查询：查询某个形状中的点；

- 形状的表示
	1. $box: 矩形，使用 `{$box:[[<x1>,<y1>],[<x2>,<y2>]]}`表示.
	2. $center: 圆形，使用`{$center:[[<x1>,<y1>],r]}`表示.
	3. $polygon: 多边形，使用`{$polygon:[[<x1>,<y1>],[<x2>,<y2>],[<x3>,<y3>]]}`表示.

- mongoDB中位置表示方式：经纬度[经度，纬度]，取值范围：经度[-180,180] 纬度[-90,90]


- demo 
```js
db.location.ensureIndex({"w":"2d"}) //创建索引
db.location.insert({w:[1,1]})
db.location.insert({w:[1,2]})
db.location.insert({w:[100,2]})
db.location.insert({w:[180,80]})
db.location.insert({w:[180,90]})

db.location.find({w:{$near:[1,1]}}) //查询
db.location.find({w:{$near:[1,1],$maxDistance:10}}) //查询最大距离为10的点
db.location.find({w:{$near:[1,1],$maxDistance:10, $minDistance:2}}) //最小距离为2,最大距离为10的点。

db.location.find({w:{$geoWithin:{$box:[[0,0],[3,3]]}}})//$geoWithin $box查询
db.location.find({w:{$geoWithin:{$center:[[0,0],5]}}})//$geoWithin $center查询
db.location.find({w:{$geoWithin:{$polygon:[[0,0],[0,1],[2,5],[6,1]]}}})//$geoWithin $polygon
```


- geoNear查询
geoNear使用runCommand命令进行使用，常用使用如下：		
```js
db.runCommand(
	{
		geoNear:<collection>,
		near:[x,y],
		minDistance:(对2d索引无效)，
		maxDistance:
		num:
		...
	}
)
```


### 2dsphere索引
概念： 球面地理位置索引		
创建方式： `db.collection.ensureIndex({w:"2dsphere"})`		
位置表示方式：		
GeoJSON: 描述一个点，一条直线，多边形等形状。		
格式：	
`{type: "", coordinates:[<coordinates>]}`		

查询方式与2d索引查询方式类似，支持`$minDistance`与`$maxDistance`		


### 索引构建情况分析
- 索引好处：加快索引相关的查询。
- 索引不好处：增加磁盘空间消耗，降低写入性能。

#### mongostat 
- mongostat:查看mongodb运行状态的程序。	
- 使用说明：mongostat -h 127.0.0.1:12345
- 字段说明：
- 索引情况：idx miss

demo:	
```sh
./bin/mongostat -h 127.0.0.1:12345 #查看mongodb的运行情况
```


#### profile集合
```js
db.getProfilingStatus() //查看prorile状态
db.getProfilingLevel() //

db.setProfilingLevel(2) //设置profile级别 设置后会自动生成system.profile表
db.system.profile.find().sort({$natural:-1}).limit(1)//在profile上作查询

```

#### 日志
- 在conf/mongod.conf 中加入verbose = vvvvv 让mongodb记录详细的日志v越多越详细

#### explain()

```sh
db.imooc_2.find({x:1}).explain() #显示查询是否使用索引等信息
```


## MongoDB安全楔子

### MongoDB安全概览
- 最安全的是物理隔离：不现实
- 网络隔离其次
- 防火墙再其次
- 用户名密码在最后

### 开启权限认证
1. auth开启
```sh
ps -ef | grep mongod | grep 12345 #查询正在运行的mongodb进程
kill <进程号> #杀掉mongodb进程

vim conf/mongod.conf #打开配置文件
auth = true #在打开的配置文件中加入这句即可开启权限认证
```


2. keyfile开启

### MongoDB 创建用户
- 创建语法: createUser (2.6之前为addUser)
- `{user:"<name>",pwd:"<cleartext password>", customData:{<any infomation>}, roles:[{role:"<role>", db:"<database>"}]}`
- 角色类型：内建类型(read, readWrite, dbAdmin, dbOwner, userAdmin)

demo: 		
	
	db.createUser({user:"imooc",pwd:"imooc",roles:[{role:"userAdmin",db:"admin"},{role:"read",db:"test"}]})


### MongoDB 用户角色
1. 数据库角色(read, readWrite, dbAdmin, dbOwner, userAdmin)
2. 集群角色(clusterAdmin, clusterManager...)
3. 备份角色(backup, restore...)
4. 其他特殊权限(DBAdminAnyDatabase...)

mongoDB 可以自己创建角色


