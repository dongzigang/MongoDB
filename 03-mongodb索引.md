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
不清楚。。。。
```

```

<h3>复合索引</h3>

```

```

<h3>过期索引</h3>

```

```

<h3>全文索引</h3>

```

```

<h3>地理位置索引</h3>

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

<h3></h3>

```

```

<h3></h3>

```

```


