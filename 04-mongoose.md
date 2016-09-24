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
// 添加 mongoose 静态方法，静态方法在Model层就能使用
client_info.statics.findByAge=function(age,callback){
	return this.model('client_collection').find({age:age},callback);
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

















