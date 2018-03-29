# JavaScript对象模式
## 1. 工厂模式
> 工厂模式是传统设计模式之一，即通过一个“工厂”对象来“生产”对象实例。
```javascript
function peopleFactory(name,age,job){
  //工厂类
  var object = new Object(); //产生一个对象
  object.name = name; //为对象属性赋值
  object.age = age;
  object.job = job;
  object.sayMyName = function(){
    console.log("我的名字叫"+name);
  }
  return object;
}
```
### 1.1 Usage
```javascript
  var people1 = peopleFactory("小明",12,"学生");
```
### 1.2 优缺点
> 优点：与其他语言设计模式的思维相同，方便理解。  
> 缺点： **生成的对象无法识别（或者说该对象就只能是Object类的实例）**  
## 2. 构造函数模式
> 为了解决识别对象的问题，因此提出构造函数模式，即直接定义构造函数来定义新类（虽说仍然是以Object类为父类）。
```javascript
function People(name,age,job){
  //People类
  this.name = name;//定义该对象的属性
  this.age = age;
  this.job = job;
  this.sayName = function(){
    console.log("我的名字叫"+this.name);
  }
}
```
### 2.1 Usage
```javascript
  var people2 = new People("小明",12,"学生");
```
### 2.2 优缺点
> 优点：解决了识别对象问题，加入了constructor方法来识别对象，可以用instanceof来判断是否是为People对象的实例,  
> 如下：
```javascript
> people2.constructor == People
< true
> people2 instanceof People
< true
```
> 缺点：  
> 1. 事实上这是加了一层constructor的语法糖，
> ```javascript
> //作为构造函数使用：  
> var o = new People("Name",12,"Job");
> o.sayMyName();
> //作为函数使用，则将对象写在全局对象：
> People("Name",12,"Job");
> window.sayMyName();
> ```  
> 2. 每次`new`后产生的新对象无论是属性还是方法都是全新独立的  
> ```javascript
> var o1 = new People("Name",12,"Job");
> var o2 = new People("Name",12,"Job");
> < o1.sayMyName == o2.sayMyName
> > false
> ```

## 3.**原型模式**
> 对象的prototype属性为指针对象，指向原型对象，将共用的属性放在prototype中，可以让所有实例共用。
```javascript
function People(){
  //People类
}
People.prototype.name = "NAME";
People.prototype.age = "AGE";
People.prototype.job = "JOB";
People.prototype.sayMyName = function(){
      console.log("我的名字叫"+this.name);
}
```
### 3.1 Usage
```javascript
  var people3 = new People();
```
```javascript
  > people3.job;
  < "JOB"
  > people3.sayMyName();
  < "我的名字叫NAME"
```
### 3.2 简易写法
```javascript
function People(){
}
People.prototype = {
  //constructor 手动将constructor赋值People对象以保证对象识别
  //若不设置，则 new People() instanceof People >> false
  //但此刻会将constructor属性设置为Enmuerable
  //即会导致使用for in会遍历出来Constructor
  constructor : People,
  name : "NAME",
  age : "AGE",
  job : "JOB",
  sayMyName: function(){
      console.log("我的名字叫"+this.name);
  }
}
```

```javascript
> var p  =new People()
> for(i in p){
  console.log(i)
  }
< constructor
< name
< age
< job
< sayMyName
```

#### 3.2.1 Fixed修复写法
> 由于简易写法会导致constructor被遍历出来，故此用简易写法最好用defineProperty来定义constructor而非直接为constructor赋值。// 有兼容性问题 
```JavaScript
function People(){
}
People.prototype = {
  name : "NAME",
  age : "AGE",
  job : "JOB",
  sayMyName: function(){
      console.log("我的名字叫"+this.name);
  }
}
Object.defineProperty(People.prototype,"constructor",{
  enumerable:false,
  value:People
})
```
```javascript
> var p  =new People()
> for(i in p){
    console.log(i)
  }
< name
< age
< job
< sayMyName
```

### 3.3 优缺点
> 优点：  
> 缺点：
> 1. Prototype的添加是非动态的，即若在new完实例后修改原型，已生成的实例是不会继承新属性/方法的(P157)  
> 2. 纯原型模式无法为实例赋不同值（因为原型的本质是属性共享的，适用于对类方法的定义）
> 

## 4.组合模式（构造+原型）
这个模式即前两个模式的组合使用，根据需求分别使用构造和原型模式定义属性及函数，通常属性将用构造方法的模式定义，原型模式来定义方法。
### Usage
```JavaScript
function People(name,age,job){
  this.name = name;
  this.age = age;
  this.job = job;
}
People.prototype = {
  constructor:People,
  sayMyName: function(){
      console.log("我的名字叫"+this.name);
  }
}
```
## 5.动态原型
这个模式仅仅是解决了OO语言开发人员觉得独立的构造函数和原型封装性不够优雅的问题。即相当于增加封装性的组合模式。
### Usage
```JavaScript
function People(name,age,job){
  this.name = name;
  this.age = age;
  this.job = job;
  if(typeof this.sayMyName != "function"){
    People.prototype.sayMyName = function(){
      console.log("我的名字叫"+this.name);
    }
  }
}
```
## 6.寄生模式
这个模式封装了创建对象的过程，无法识别对象所属，多用于需要在某个原生函数上进行修改和优化而又不能修改原函数的情况。修改原型不会影响对象。
### Usage
```javascript
//
function myArray(){
  //创建数组对象
  var values = new Array();
  //添加值
  values.push.apply(values,arguements);
  //新增方法
  values.toPipedString = function(){
    return this.join("|");
  }
  return values;
}
```
## 7.稳妥构造函数模式
稳妥就是无公共属性，且方法也不引用this对象，亦不使用new来调用构造函数。
### Usage
```javascript
function People(name,age,job){
  //无引用this对象
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayMyName = function(){
    console.log("我的名字叫"+name);
  }
  return o;
}
```
```javascript
//不使用new
var p = People("NAME",12,"JOB");
```