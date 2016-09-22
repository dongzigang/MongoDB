<h2>mongodb索引</h2>
<h3>创建索引</h3>

```
db.collectionName.createIndex({name:1})

db.collectionName.createIndexes([keypatterns], <options>)
```
<h3>删除索引</h3>
```
 db.collectionName.dropIndex(index) 删除单个
 db.collectionName.dropIndexes()    删除所有
```
<h3>_id索引</h3>
_id索引是绝大多数集合默认创建的索引

只要集合中有数据
```
db.collectionName.getIndexes()
```
都会显示出_id索引

<h3>单键索引</h3>

```
db.collectionName.createIndex({x:1})
```

<h3>多键索引</h3>

栗子

假设有一个名为 inventory 的集合包含了如下文档
```
{ _id: 5, type: "food", item: "aaa", ratings: [5,8,9]}
{ _id: 6, type: "food", item: "bbb", ratings: [5,9]}
{ _id: 7, type: "food", item: "ccc", ratings: [9,5,8]}
{ _id: 8, type: "food", item: "ddd", ratings: [9,5]}
{ _id: 9, type: "food", item: "eee", ratings: [5,9,5]}
```
集合将 ratings 这个字段设为多键索引(multikey index):
```
db.inventory.createIndex( { ratings: 1 } )
```
以下的操作将查询 ratings 字段的值为 [5,9] 的文档：
```
db.inventory.find( { ratings: [ 5, 9 ] } )
```
MongoDB 可以通过多键索引查找出 ratings 这个数组中任何位置包含有5这个元素的文档，然后，再检索这些文档，筛选出 ratings 的值完全等于 [5,9] 的这些文档。


<h3>复合索引</h3>
当我们查询条件不止一个时，就需要建立复合索引
创建
```
db.demo.createIndex( { "name": 1, "sex": 1 } )
```
使用
```
sb.demo.find({name:"deg",sex:"man"})
```

<h3>过期索引</h3>
一段时间后会过期的索引，索引过期后，相应数据会被自动删除

适合存储在一段时间后会失效的数据，比如用户登陆信息，存储日志

存储在过期索引字段的值必须是指定的时间类型（ISODate或ISODate数组）不能使用时间戳，否则不能自动删除

如果使用ISODate数组则按照最小的时间进行删除

过期索引不能是复合索引

删除时间是不精确的，删除过程由后台程序60s跑一次，而且删除也需要一些时间，蓑衣存在误差
```
db.demo.createIndex({time:new Date()},expireAfterSounds:30)  指定30秒后删除索引

>db.demo.insert({time:new Date()})  大概60秒左右被删除

>db.demo.insert({time:1})   不会被自动删除
```

<h3>全文索引</h3>
全文索引暂时不支持中文

创建全文索引

key:字段名

value:固定字符串"text"
```
db.demo.createIndex({key:"text"})

db.demo.createIndex({key_1:"text",key_2:"text"})

db.demo.createIndex({"$**":"text"})    不同字段搜索相同内容

```
栗子

创建索引
```
>db.demo.createIndex({"title":"text"})    
```
插入数据
```
>db.demo.insert({"title":"I love"})

>db.demo.insert({"title":"I love you"})

>db.demo.insert({"title":"I love you,deg"})

>db.demo.insert({"title":"I love you ,deg momoda"})
```
查询
```
db.demo.find({$text:{$search:"I"}})  包含 I

db.demo.find({$text:{$search:"I love you"}})   包含 I love you 三个字符串之一

db.demo.find({$text:{$search:"I love -you"}})  同时包含 I love 两个字符串之一 但不包含you

db.demo.find({$text:{$search:" \"I\" \"love\" \"you\" "}})  同时包含 I love you 三个字符串 
```
<h4>全文索引相似度查询</h4>
```
>db.demo.find({$text:{$search:"I"}},{score:{$meta:"textScore"}})

{ "_id" : ObjectId("561b2efa1d7564ee57e78246"), "title" : "I", "score" : 1.875 }

{ "_id" : ObjectId("561b2f021d7564ee57e78247"), "tiele" : "I love you", "score" : 1.25 }
```
<h4>按照相似度排序</h4>
```
>db.demo.find({$text:{$search:"I"}},{score:{$meta:"textScore"}}).sort({score:{$meta:"textScore"}})

{ "_id" : ObjectId("561b2efa1d7564ee57e78246"), "title" : "I", "score" : 1.875 }

{ "_id" : ObjectId("561b2f021d7564ee57e78247"), "tiele" : "I love you", "score" : 1.25 }
```
<h4>全文索引使用的限制</h4>

每次只能指定一个$text查询

$text查询不能出现在$nor查询中

查询中如果包含了$text，强制查询hint就不再起作用


<h3>地理位置索引</h3>

概念：将一些点的位置存储在mongodb中，创建索引索引后可以按照位置来查找其他点

子分类：2d索引用于存储和查找平面上的点
        
        2dsphere索引，用于存储和查找球面上的点

查找方式：

   1.查找距离某个点一定距离内的点
   
   2.查找某个区域内的点
   
   3........

<h4>2d索引</h4>
创建
```
db.demo.createIndex({"w":"2d"})
```
位置表示方式：经纬度[经度，纬度]

取值范围：经度[-180,180]
          
          纬度[-90,90]

插入数据
```
db.demo.insert({w:[120,45]})
db.demo.insert({w:[121,45]})
db.demo.insert({w:[122,45]})
db.demo.insert({w:[123,45]})
```
查询方式

1.$near查询：查询距离某个点最近的100个点

```
>db.demo.find({w:{$near:[120,45]}})
结果返回刚才插入的所有点

>db.demo.find({w:{$near:[120,45],$maxDistance:2}})
返回距离[120,45]最近的2个点

```
2.$geoWithin查询：查询某个形状内的点

形状表示：

1.$box:矩形
  
  {$box:[[<x1>,<y1>],[<x2>,<y2>]]}

2.$center:圆形
  
  {$center:[[<x>,<y>],r]}

3.$polygon:多边形

  {$polygon:[[<x1>,<y1>],[<x2>,<y2>],[<x3>,<y3>]]}

```
db.demo.find({$box:[[<80>,<90>],[<100>,<120>]]})
```

<h3>索引属性</h3>
比较重要的属性有：

名字 name

在创建索引时monggodb会自动为索引创建一个名字键名下划线（key_1）

如果想指定索引名如下
```
db.demo.createIndex({},{name:"xxx"})
```
在删除索引时也可以使用索引名

```
db.demo.dropIndex("name_1");
```

唯一性 unique

```
>db.demo.createIndex({m:1,n:1},{unique:ture})     创建唯一性索引
>db.demo.insert({m:1,n:2})
插入成功
>db.demo.insert({m:1,n:2})
插入失败（已经存在插入有，不存在不插入）
```

稀疏性   sparse

假如集合文档中不包含x字段，而你创建了一个x字段上的索引，在默认情况下，该索引会被创建，如果使用了sparse:ture则不会被创建，这样就节省了资源
```
db.demo.createIndex({},{sparse:ture})
```
栗子

```
>db.demo.insert({m:1})

>db.demo.insert({n:1})

>db.demo.find({m:{$exists:true}})    查找存在m字段的文档
查找出第一条插入的数据

db.demo.createIndex({m:1},{sparse:ture}) 创建m字段稀疏性索引

>db.demo.find({m:{$exists:false}}) 
查找出了  n   字段，因为用的不是m字段稀疏性索引

>db.demo.find({m:{$exists:false}}) .hint("m_1")  强制使用m字段稀疏性索引
没有返回内容
```

过期索引 expireAfterSounds  

```
db.demo.createIndex({time:new Date()},expireAfterSounds:30)
```
<h3></h3>

```

```

<h3></h3>

```

```

<h3></h3>

```

```

<h3></h3>

```

```


