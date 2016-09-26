这他妈一学起来就没完没了，本来我就想用nodejs搭个网站，结果有的没得学了一大堆,学编程就是一无底洞

——————前言by DEG

<h2>mongoose</h2>

作用：用于连接node.js与MongoDB

安装
```
npm install mongoose --save
```

在node.js脚本中连接MongoDB数据库

```js
var mongoose=require('mongoose');

// 连接字符串格式为mongodb://主机/数据库名
var db= mongoose.createConnection('mongodb://127.0.0.1:27017/test')
```
注意，运行上面这个脚本时，必须确保MongoDB处于运行中

数据库连接后，可以对open和error事件指定监听函数

```js
//这里他妈就测了一下有没有连接成功
var db = mongoose.connection;

db.on('error', function callback () {
  console.log("Connection error");
});

db.once('open', function callback () {
  console.log("Mongo working!");
});
```
mongoose.Schema方法用来定义数据集的格式（schema）
```js
var Schema = mongoose.Schema;
var userSchema = new Schema({
  name : String,
  age : Number,
  DOB : Date,
  isAlive : Boolean
});
```
Schema.Type
```js
var schema = new Schema({
  name:    String,
  binary:  Buffer,
  living:  Boolean,
  updated: { type: Date, default: Date.now },
  age:     { type: Number, min: 18, max: 65 },
  mixed:   Schema.Types.Mixed,
  _someId: Schema.Types.ObjectId,
  array:      [],
  ofString:   [String],
  ofNumber:   [Number],
  ofDates:    [Date],
  ofBuffer:   [Buffer],
  ofBoolean:  [Boolean],
  ofMixed:    [Schema.Types.Mixed],
  ofObjectId: [Schema.Types.ObjectId],
  nested: {
    stuff: { type: String, lowercase: true, trim: true }
  }
})

```
mongoose.model方法将格式分配给指定的数据集
```
//把刚才定义的数据格式指向一个名叫user的集合collection
var User = mongoose.model('User', userSchema);
```
//创建一个Schema实例(Entity)
```js
var arvind = new User({
  name : 'Arvind',
  age : 99,
  DOB : '01/01/1915',
  isAlive : true
});

//保存数据
arvind.save(function (err, data) {
  if (err){
    console.log(err);
  } else {
    console.log('Saved : ', data );
  }
});

```
<h3>mongoose实现增删查改</h3>

```js
//连接到数据库
var mongoose=require('mongoose');
var db=mongoose.createConnection('mongodb://127.0.0.1/demo');
//创建Schema
var client_info=new mongoose.Schema({
	name:String,
	age:{ type: Number, min: 18, max: 65 },
	birthday: Number,
	address:String,
	phone:Number
})
//model
var client_info_model=db.model('client_collection',client_info);
```
<br><br><br><br>
<h4>基于Entity操作</h4>
增加信息
```js
//Entity
var doc={
	name:'deg',
	age:18,
	birthday:1994,
	address:'china',
	phone:13202629601
}
var client_info_entity=new client_info_model(doc);
//保存数据
client_info_entity.save(function(error){
	if(error){
		console.log(error);
	}
	else{
		console.log('save success')
	}
	//关闭数据连接
	db.close()
})
```
查询信息
```js
//为client_info添加查询方法
client_info.methods.findByName=function(name,callback){
	return this.model('client_collection').find({name:name},callback);
}

//Entity
var client_info_entity=new client_info_model({});
client_info_entity.findByName('deg',function(error,result){
	if(error){
		console.log(error);
	}
	else{
		console.log(result);
	}
	db.close();
})
```
<h4>基于model的增加、修改与查询</h4>

添加信息
```js
var doc={
	name:'deg',
	age:18,
	birthday:1994,
	address:'china',
	phone:13202629601
}
client_info_model.create(doc,function(error){
	if(error){
		console.log(error);
	}
	else{
		colsole.log('save success');
	}
	db.close();
})
```
修改信息
```js
var conditions = {name : 'deg'};
var update     = {$set : {age : 27, address:'english'}};
var options    = {upsert : true};
client_info_model.update(conditions, update, options, function(error){
    if(error) {
        console.log(error);
    } else {
        console.log('update ok!');
    }
    //关闭数据库链接
    db.close();
});
```
查询信息

查询到的所有
```js
client_info_model.find(function(err,result){

    if(error) {
        console.log(error);
    } else {
        console.log(result);
    }
    //关闭数据库链接
    db.close();
});
```
```js
// mongoose find
var criteria = {title : 'emtity_demo_title'}; // 查询条件
var fields   = {title : 1, content : 1, time : 1}; // 待返回的字段
var options  = {};
client_info_model.find(criteria, fields, options, function(error, result){
    if(error) {
        console.log(error);
    } else {
        console.log(result);
    }
    //关闭数据库链接
    db.close();
});
```
删除信息
```js
var conditions = {name: 'deg'};
client_info_model.remove(conditions, function(error){
    if(error) {
        console.log(error);
    } else {
        console.log('delete ok!');
    }

    //关闭数据库链接
    db.close();
});
```
<h4>基于静态方法的查询</h4>
```js
// 添加 mongoose 静态方法，静态方法在Model层就能使用
client_info.statics.findByAge=function(age,callback){
	return this.model('client_collection').find({age:age},callback);
}
// 基于静态方法的查询
client_info_model.findbytitle('emtity_demo_title', function(error, result){
    if(error) {
        console.log(error);
    } else {
        console.log(result);
    }
    //关闭数据库链接
    db.close();
});
```

<h3>查询方法</h3>

Mongoose 模型提供了 find, findOne, 和 findById 方法用于文档查询。

1.Model.find
```
Model.find(query, fields, options, callback)
// fields 和 options 都是可选参数

简单查询
Model.find({ 'csser.com': 5 }, function (err, docs) {
	// docs 是查询的结果数组 
});

只查询指定键的结果
Model.find({}, ['first', 'last'], function (err, docs) {
  // docs 此时只包含文档的部分键值
})
```

2.Model.findOne

与 Model.find 相同，但只返回查询到的第一个文档
```
Model.findOne({ age: 5}, function (err, doc){
  // doc 是单个文档
});
```
Model.findById

与 findOne 相同，但它接收文档的 _id 作为参数，返回单个文档。_id 可以是字符串或 ObjectId 对象。
```
Model.findById(obj._id, function (err, doc){
  // doc 是单个文档
});
```
Model.count

返回符合条件的文档数。
```
Model.count(conditions, callback);
```
Model.remove

删除符合条件的文档。
```
Model.remove(conditions, callback);
```
Model.distinct

查询符合条件的文档并返回根据键分组的结果。
```
Model.distinct(field, conditions, callback);
```
Model.where

当查询比较复杂时，用 where：
```
Model
.where('age').gte(25)
.where('tags').in(['movie', 'music', 'art'])
.select('name', 'age', 'tags')
.skip(20)
.limit(10)
.asc('age')
.slaveOk()
.hint({ age: 1, name: 1 })
.run(callback);
```
Model.$where

有时我们需要在 mongodb 中使用 JavaScript 表达式进行查询，这时可以用 find({$where : javascript}) 方式，$where 是一种快捷方式，并支持链式调用查询。
```
Model.$where('this.firstname === this.lastname').exec(callback)
```
Model.update

使用 update 子句更新符合指定条件的文档，更新数据在发送到数据库服务器之前会改变模型的类型。
```
var conditions = { name: 'borne' };
var update = { $inc: { visits: 1 }}; 
var options = { multi: true };
Model.update(conditions, update, options, callback)
```
注意：为了向后兼容，所有顶级更新键如果不是原子操作命名的，会统一被按 $set 操作处理，例如：
```
var query = { name: 'borne' };
Model.update(query, { name: 'jason borne' }, options, callback)
```
会被这样发送到数据库服务器
```
Model.update(query, { $set: { name: 'jason borne' }}, options, callback)
```
查询 API

如果不提供回调函数，所有这些方法都返回 Query 对象，它们都可以被再次修改（比如增加选项、键等），直到调用 exec 方法。
```
var query = Model.find({});

query.where('field', 5);
query.limit(5);
query.skip(100);

query.exec(function (err, docs) {
  // called when the `query.complete` or `query.error` are called
  // internally
});
```










