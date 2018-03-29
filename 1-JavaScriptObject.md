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
  //即会导致
  constructor : People,
  name : "NAME",
  age : "AGE",
  job : "JOB",
  sayMyName: function(){
      console.log("我的名字叫"+this.name);
  }
}
```


### 3.3 优缺点
> 