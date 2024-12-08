---
title: 设计模式
---

## 基础知识

原型模式不单是一种设计模式，也被称为一种编程范型。

### 使用克隆的原型模式

从设计模式的角度讲，原型模式是用于创建对象的一种模式，如果我们想要创建一个对象，一种方法是先指定它的类型，然后通过类来创建这个对象。原型模式选择了另外一种方式，我们不再关心对象的具体类型，而是找到一个对象，然后通过克隆来创建一个一模一样的对象。

原型模式的实现关键，是语言本身是否提供了`clone`方法。ECMAScript 5提供了`Object.create`方法，可以用来克隆对象。

```javascript
const Animal = function () {}
const animal = new Animal()
const cloneAnimal = Object.create(animal)
Object.create = Object.create || function (obj) {
  const F = function () {}
  F.prototype = obj
  return new F()
}
```

从设计模式的角度来讲，原型模式的意义并不算大。但JavaScript本身是一门基于原型的面向对象语言，它的对象系统就是使用原型模式来搭建的，在这里称之为原型编程范型也许更合适。

### this指向

除去不常用的`with`和`eval`的情况，具体到实际应用中，this的指向大致可以分为以下4种。
- 作为对象的方法调用。
- 作为普通函数调用。
- 构造器调用。
- Function.prototype.call或Function.prototype.apply调用。

#### 作为对象的方法调用
当函数作为对象的方法被调用时，this指向该对象。

#### 作为普通函数调用

作为普通函数调用,此时的this总是指向全局对象,在浏览器里this指向`window`,严格模式下为`undefined`。

#### 构造器调用

当用new运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的this就指向返回的这个对象。如果显式返回一个对象，那么this将指向这个对象。
```javascript
const MyClass = function () {
  this.name = 'sven'
  return { // 显式地返回一个对象
    name: 'anne'
  }
}

const obj = new MyClass()
console.log (obj.name) // 输出：anne
```

#### Function.prototype.call或Function.prototype.apply调用

跟普通的函数调用相比，用Function.prototype.call或Function.prototype.apply可以动态地改变传入函数的this。

apply接受两个参数，第一个参数指定了函数体内this对象的指向，第二个参数为一个带下标的集合，这个集合可以为数组，也可以为类数组，apply方法把这个集合中的元素作为参数传递给被调用的函数。

call传入的参数数量不固定，跟apply相同的是，第一个参数也是代表函数体内的this指向，从第二个参数开始往后，每个参数被依次传入函数。

call是包装在apply上面的一颗语法糖，如果我们明确地知道函数接受多少个参数，而且想一目了然地表达形参和实参的对应关系，那么也可以用call来传送参数。

### 闭包和高阶函数

#### 闭包

闭包的形成与变量的作用域以及变量的生存周期密切相关。

##### 变量的作用域

在JavaScript中，函数可以用来创造函数作用域。此时的函数像一层半透明的玻璃，在函数里面可以看到外面的变量，而在函数外面则无法看到函数里面的变量。这是因为当在函数中搜索一个变量的时候，如果该函数内并没有声明这个变量，那么此次搜索的过程会随着代码执行环境创建的作用域链往外层逐层搜索，一直搜索到全局对象为止。变量的搜索是从内到外而非从外到内的。

##### 变量的生存周期

对于在函数内用var关键字声明的局部变量来说，当退出函数时，这些局部变量即失去了它们的价值，它们都会随着函数调用的结束而被销毁。下次调用时就会产生新的生命周期。

```javascript
const func = function () {
  const a = 1 // 退出函数后局部变量a将被销毁
  console.log (a)
}

func()
```

当使用闭包保存变量时:
```javascript
const func = function () {
  let a = 1
  return function () {
    a++
    console.log (a)
  }
}

const f = func()

f() // 输出：2
f() // 输出：3
f() // 输出：4
f() // 输出：5
```
此时func函数执行完毕将抛出一个匿名函数的引用, 它可以访问到func()被调用时产生的环境，既然局部变量所在的环境还能被外界访问，这个局部变量就有了不被销毁的理由。在这里产生了一个闭包结构，局部变量的生命看起来被延续了。

利用闭包特性编写的代码。
```javascript
const Type = {}
for (var i = 0, type; type = ['String', 'Array', 'Number'][i++];) {
  (function (type) {
    Type[`is${type}`] = function (obj) {
      return Object.prototype.toString.call(obj) === `[object ${type}]`
    }
  })(type)
};

Type.isArray([]) // 输出：true
Type.isString('str') // 输出：true
```

使用闭包的特性实现命令模式。
```html
<html>
  <body>
    <button id="execute">点击我执行命令</button>
    <button id="undo">点击我执行命令</button>
    <script>
      var Tv = {
        open: function() {
          console.log("打开电视机");
        },
        close: function() {
          console.log("关上电视机");
        },
      };

      var createCommand = function(receiver) {
        var execute = function() {
          return receiver.open(); // 执行命令，打开电视机
        };

        var undo = function() {
          return receiver.close(); // 执行命令，关闭电视机
        };

        return {
          execute: execute,
          undo: undo,
        };
      };

      var setCommand = function(command) {
        document.getElementById("execute").onclick = function() {
          command.execute(); // 输出：打开电视机
        };
        document.getElementById("undo").onclick = function() {
          command.undo(); // 输出：关闭电视机
        };
      };

      setCommand(createCommand(Tv));
    </script>
  </body>
</html>
```

#### 高阶函数

高阶函数是指至少满足下列条件之一的函数。
- 函数可以作为参数被传递；
- 函数可以作为返回值输出。

##### 高阶函数实现AOP

AOP（面向切面编程）的主要作用是把一些跟核心业务逻辑模块无关的功能抽离出来，这些跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这些功能抽离出来之后，再通过“动态织入”的方式掺入业务逻辑模块中。这样做的好处首先是可以保持业务逻辑模块的纯净和高内聚性，其次是可以很方便地复用日志统计等功能模块。

动态织入示例。

```javascript
Function.prototype.before = function (beforefn) {
  const __self = this // 保存原函数的引用
  return function () {
    // 返回包含了原函数和新函数的"代理"函数
    beforefn.apply(this, arguments) // 执行新函数，修正this
    return __self.apply(this, arguments) // 执行原函数
  }
}

Function.prototype.after = function (afterfn) {
  const __self = this
  return function () {
    const ret = __self.apply(this, arguments)
    afterfn.apply(this, arguments)
    return ret
  }
}

let func = function () {
  console.log(2)
}
func = func
  .after(() => {
    console.log(3)
  })
  .before(() => {
    console.log(1)
  })

func()
```

函数柯里化(currying)。
```javascript
const currying = function (fn) {
  const args = []

  return function () {
    if (arguments.length === 0) {
      return fn.apply(this, args)
    }
    else {
      [].push.apply(args, arguments)
      return arguments.callee
    }
  }
}

var cost = (function () {
  let money = 0

  return function () {
    for (let i = 0, l = arguments.length; i < l; i++) {
      money += arguments[i]
    }
    return money
  }
})()

var cost = currying(cost) // 转化成currying函数

cost(100) // 未真正求值
cost(200) // 未真正求值
cost(300) // 未真正求值
console.log(cost()) // 求值并输出：600
```

反柯里化(uncurrying)。
```javascript
Function.prototype.uncurrying = function () {
  const self = this
  return function () {
    const obj = Array.prototype.shift.call(arguments)
    return self.apply(obj, arguments)
  }
}
const push = Array.prototype.push.uncurrying();

(function () {
  push(arguments, 4)
  console.log(arguments) // 输出：[1, 2, 3, 4]
})(1, 2, 3)
```

另外一种实现方式：
```javascript
Function.prototype.uncurrying = function () {
  const self = this
  return function () {
    return Function.prototype.call.apply(self, arguments)
  }
}
```

函数节流:
```javascript
const throttle = function (fn, interval) {
  const __self = fn // 保存需要被延迟执行的函数引用
  let timer // 定时器
  let firstTime = true // 是否是第一次调用

  return function () {
    const args = arguments
    const __me = this

    if (firstTime) { // 如果是第一次调用，不需延迟执行
      __self.apply(__me, args)
      return firstTime = false
    }

    if (timer) { // 如果定时器还在，说明前一次延迟执行还没有完成
      return false
    }

    timer = setTimeout(() => { // 延迟一段时间执行
      clearTimeout(timer)
      timer = null
      __self.apply(__me, args)
    }, interval || 500)
  }
}

window.onresize = throttle(() => {
  console.log(1)
}, 500)
```

分时函数:
```javascript
const timeChunk = function (ary, fn, count) {
  let obj,
    t

  const len = ary.length

  const start = function () {
    for (let i = 0; i < Math.min(count || 1, ary.length); i++) {
      const obj = ary.shift()
      fn(obj)
    }
  }

  return function () {
    t = setInterval(() => {
      if (ary.length === 0) { // 如果全部节点都已经被创建好
        return clearInterval(t)
      }
      start()
    }, 200) // 分批执行的时间间隔，也可以用参数的形式传入
  }
}

const ary = []

for (let i = 1; i <= 1000; i++) {
  ary.push(i)
};

const renderFriendList = timeChunk(ary, (n) => {
  const div = document.createElement('div')
  div.innerHTML = n
  document.body.appendChild(div)
}, 8)

renderFriendList()
```

惰性加载函数:

```html
<html>
    <body>
      <div id="div1">点我绑定事件</div>
    <script>

      var addEvent = function( elem, type, handler ){
        if ( window.addEventListener ){
            addEvent = function( elem, type, handler ){
                elem.addEventListener( type, handler, false );
            }
        }else if ( window.attachEvent ){
            addEvent = function( elem, type, handler ){
                elem.attachEvent( 'on' + type, handler );
            }
        }

        addEvent( elem, type, handler );
      };
      var div = document.getElementById( 'div1' );

      addEvent( div, 'click', function(){
          console.log (1);
      });

      addEvent( div, 'click', function(){
          console.log (2);
      });

    </script>
    </body>
</html>
```

## 单例模式

单例模式的定义是：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

### 实现单例模式

要实现一个标准的单例模式并不复杂，无非是用一个变量来标志当前是否已经为某个类创建过对象，如果是，则在下一次获取该类的实例时，直接返回之前创建的对象。代码如下：

```javascript
const Singleton = function (name) {
  this.name = name
  this.instance = null
}

Singleton.prototype.getName = function () {
  console.log (this.name)
}

Singleton.getInstance = function (name) {
  if (!this.instance) {
    this.instance = new Singleton(name)
  }
  return this.instance
}

const a = Singleton.getInstance('sven1')
const b = Singleton.getInstance('sven2')

console.log (a === b) // true
```

或：

```javascript
const Singleton = function (name) {
  this.name = name
}

Singleton.prototype.getName = function () {
  console.log (this.name)
}

Singleton.getInstance = (function () {
  let instance = null
  return function (name) {
    if (!instance) {
      instance = new Singleton(name)
    }
    return instance
  }
})()

const a = Singleton.getInstance('sven1')
const b = Singleton.getInstance('sven2')

console.log (a === b) // true
```

### 透明的单例模式

我们现在的目标是实现一个“透明”的单例类，用户从这个类中创建对象的时候，可以像使用其他任何普通类一样。

```javascript
const CreateDiv = (function () {
  let instance

  const CreateDiv = function (html) {
    if (instance) {
      return instance
    }
    this.html = html
    this.init()
    return instance = this
  }

  CreateDiv.prototype.init = function () {
    const div = document.createElement('div')
    div.innerHTML = this.html
    document.body.appendChild(div)
  }

  return CreateDiv
})()

const a = new CreateDiv('sven1')
const b = new CreateDiv('sven2')

alert (a === b) // true
```

### 用代理实现单例模式

```javascript
const CreateDiv = function (html) {
  this.html = html
  this.init()
}

CreateDiv.prototype.init = function () {
  const div = document.createElement('div')
  div.innerHTML = this.html
  document.body.appendChild(div)
}
```

接下来引入代理类proxySingletonCreateDiv：

```javascript
const ProxySingletonCreateDiv = (function () {
  let instance
  return function (html) {
    if (!instance) {
      instance = new CreateDiv(html)
    }

    return instance
  }
})()

const a = new ProxySingletonCreateDiv('sven1')
const b = new ProxySingletonCreateDiv('sven2')

alert (a === b)
```

### JavaScript中的单例模式

全局变量不是单例模式，但在JavaScript开发中，我们经常会把全局变量当成单例来使用。

降低全局变量的命名污染:

#### 使用命名空间

最简单的方法依然是用对象字面量的方式：

```javascript
const namespace1 = {
  a() {
    alert (1)
  },
  b() {
    alert (2)
  }
}
```

另外我们还可以动态地创建命名空间:

```javascript
var MyApp = {}

MyApp.namespace = function (name) {
  const parts = name.split('.')
  let current = MyApp
  for (const i in parts) {
    if (!current[parts[i]]) {
      current[parts[i]] = {}
    }
    current = current[parts[i]]
  }
}

MyApp.namespace('event')
MyApp.namespace('dom.style')

console.dir(MyApp)

// 上述代码等价于：

var MyApp = {
  event: {},
  dom: {
    style: {}
  }
}
```

#### 使用闭包封装私有变量

```javascript
const user = (function () {
  const __name = 'sven'
  const __age = 29

  return {
    getUserInfo() {
      return `${__name}-${__age}`
    }
  }
})()
```

### 惰性单例

惰性单例指的是在需要的时候才创建对象实例。

```javascript
Singleton.getInstance = (function () {
  let instance = null
  return function (name) {
    if (!instance) {
      instance = new Singleton(name)
    }
    return instance
  }
})()
```

假设全局使用唯一浮窗：

```html
<html>
  <body>
    <button id="loginBtn">登录</button>
  </body>

  <script>
    var createLoginLayer = (function() {
      var div;
      return function() {
        if (!div) {
          div = document.createElement("div");
          div.innerHTML = "我是登录浮窗";
          div.style.display = "none";
          document.body.appendChild(div);
        }

        return div;
      };
    })();
    document.getElementById("loginBtn").onclick = function() {
      var loginLayer = createLoginLayer();
      loginLayer.style.display = "block";
    };
  </script>
</html>
```

### 通用的惰性单例

```javascript
const getSingle = function (fn) {
  let result
  return function () {
    return result || (result = fn.apply(this, arguments))
  }
}

const bindEvent = getSingle(() => {
  document.getElementById('div1').onclick = function () {
    console.log('click')
  }
  return true
})

const render = function () {
  console.log('开始渲染列表')
  bindEvent()
}

render()
render()
render()
```

## 策略模式

策略模式的定义是：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

### 使用策略模式计算奖金

#### 最初的代码实现：

```javascript
const calculateBonus = function (performanceLevel, salary) {
  if (performanceLevel === 'S') {
    return salary * 4
  }

  if (performanceLevel === 'A') {
    return salary * 3
  }

  if (performanceLevel === 'B') {
    return salary * 2
  }
}

calculateBonus('B', 20000) // 输出：40000
calculateBonus('S', 6000) // 输出：24000
```

#### 使用组合函数重构代码

```javascript
const performanceS = function (salary) {
  return salary * 4
}

const performanceA = function (salary) {
  return salary * 3
}

const performanceB = function (salary) {
  return salary * 2
}

const calculateBonus = function (performanceLevel, salary) {
  if (performanceLevel === 'S') {
    return performanceS(salary)
  }

  if (performanceLevel === 'A') {
    return performanceA(salary)
  }

  if (performanceLevel === 'B') {
    return performanceB(salary)
  }
}

calculateBonus('A', 10000) // 输出：30000
```

#### 使用策略模式重构代码

```javascript
``

## 八种常见的设计模式

 在软件工程中，设计模式（Design Pattern）是对软件设计中普遍存在（反复出现）的各种问题，所提出的解决方案。
 根据模式的目的来划分的话，GoF（Gang of Four）设计模式可以分为以下 3 种类型：
 1. **创建型模式**：用来描述 “如何创建对象”，它的主要特点是 “将对象的创建和使用分离”。包括单例、原型、工厂方法、抽象工厂和建造者 5 种模式。

 2. **结构型模式**：用来描述如何将类或对象按照某种布局组成更大的结构。包括代理、适配器、桥接、装饰、外观、享元和组合 7 种模式。

 3. **行为型模式**：用来识别对象之间的常用交流模式以及如何分配职责。包括模板方法、策略、命令、职责链、状态、观察者、中介者、迭代器、访问者、备忘录和解释器 11 种模式。

> 原文链接
  [图解九种常见的设计模式](https://segmentfault.com/a/1190000030850326)

### 建造者模式

建造者模式（Builder Pattern）将一个复杂对象分解成多个相对简单的部分，然后根据不同需要分别创建它们，最后构建成该复杂对象。一辆小汽车 🚗 通常由 发动机、底盘、车身和电气设备 四大部分组成。汽车电气设备的内部构造很复杂，简单起见，我们只考虑三个部分：引擎、底盘和车身。在现实生活中，小汽车也是由不同的零部件组装而成，比如上图中我们把小汽车分成引擎、底盘和车身三大部分。下面我们来看一下如何使用建造者模式来造车子。

实现代码

```typescript
class Car {
  constructor(
    public engine: string,
    public chassis: string,
    public body: string
  ) {}
}

class CarBuilder {
  engine!: string // 引擎
  chassis!: string // 底盘
  body!: string // 车身

  addChassis(chassis: string) {
    this.chassis = chassis
    return this
  }

  addEngine(engine: string) {
    this.engine = engine
    return this
  }

  addBody(body: string) {
    this.body = body
    return this
  }

  build() {
    return new Car(this.engine, this.chassis, this.body)
  }
}
```

在以上代码中，我们定义一个 `CarBuilder` 类，并提供了 `addChassis`、`addEngine` 和 `addBody` 3 个方法用于组装车子的不同部位，当车子的 3 个部分都组装完成后，调用 `build` 方法就可以开始造车。

使用示例

```typescript
const car = new CarBuilder()
  .addEngine('v12')
  .addBody('镁合金')
  .addChassis('复合材料')
  .build()

console.log(car)
```

应用场景及案例

- 需要生成的产品对象有复杂的内部结构，这些产品对象通常包含多个成员属性。
- 需要生成的产品对象的属性相互依赖，需要指定其生成顺序。
- 隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同的产品。
- [https://github.com/dresende/node-sql-query](https://github.com/dresende/node-sql-query)

### 工厂模式

在现实生活中，工厂是负责生产产品的，比如牛奶、面包或礼物等，这些产品满足了我们日常的生理需求。在众多设计模式当中，有一种被称为工厂模式的设计模式，它提供了创建对象的最佳方式。工厂模式可以分为：简单工厂模式、工厂方法模式和抽象工厂模式。

#### 简单工厂
简单工厂模式又叫 **静态方法模式**，因为工厂类中定义了一个静态方法用于创建对象。简单工厂让使用者不用知道具体的参数就可以创建出所需的 “产品” 类，即使用者可以直接消费产品而不需要知道产品的具体生产细节。

实现代码

```typescript
abstract class BMW {
  abstract run(): void
}

class BMW730 extends BMW {
  run(): void {
    console.log('BMW730 发动咯')
  }
}

class BMW840 extends BMW {
  run(): void {
    console.log('BMW840 发动咯')
  }
}

class BMWFactory {
  public static produceBMW(model: '730' | '840'): BMW {
    if (model === '730') {
      return new BMW730()
    }
    else {
      return new BMW840()
    }
  }
}
```

在以上代码中，我们定义一个 BMWFactory 类，该类提供了一个静态的 `produceBMW()` 方法，用于根据不同的模型参数来创建不同型号的车子。

使用示例

```typescript
const bmw730 = BMWFactory.produceBMW('730')
const bmw840 = BMWFactory.produceBMW('840')

bmw730.run()
bmw840.run()
```

应用场景

* 工厂类负责创建的对象比较少：由于创建的对象比较少，不会造成工厂方法中业务逻辑过于复杂。
* 客户端只需知道传入工厂类静态方法的参数，而不需要关心创建对象的细节。

#### 工厂方法

工厂方法模式（Factory Method Pattern）又称为工厂模式，也叫多态工厂（Polymorphic Factory）模式，它属于类创建型模式。

在工厂方法模式中，工厂父类负责定义创建产品对象的公共接口，而工厂子类则负责生成具体的产品对象， **这样做的目的是将产品类的实例化操作延迟到工厂子类中完成**，即通过工厂子类来确定究竟应该实例化哪一个具体产品类。

```typescript
abstract class BMWFactory {
  abstract produceBMW(): BMW
}

class BMW730Factory extends BMWFactory {
  produceBMW(): BMW {
    return new BMW730()
  }
}

class BMW840Factory extends BMWFactory {
  produceBMW(): BMW {
    return new BMW840()
  }
}
```

在以上代码中，我们分别创建了 `BMW730Factory` 和 `BMW840Factory` 两个工厂类，然后使用这两个类的实例来生产不同型号的车子。

使用示例

```typescript
const bmw730Factory = new BMW730Factory()
const bmw840Factory = new BMW840Factory()

const bmw730 = bmw730Factory.produceBMW()
const bmw840 = bmw840Factory.produceBMW()

bmw730.run()
bmw840.run()
```

应用场景

- 一个类不知道它所需要的对象的类：在工厂方法模式中，客户端不需要知道具体产品类的类名，只需要知道所对应的工厂即可，具体的产品对象由具体工厂类创建；客户端需要知道创建具体产品的工厂类。
- 一个类通过其子类来指定创建哪个对象：在工厂方法模式中，对于抽象工厂类只需要提供一个创建产品的接口，而由其子类来确定具体要创建的对象，利用面向对象的多态性和里氏代换原则，在程序运行时，子类对象将覆盖父类对象，从而使得系统更容易扩展。

#### 抽象工厂

抽象工厂模式（Abstract Factory Pattern），提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。

在工厂方法模式中具体工厂负责生产具体的产品，每一个具体工厂对应一种具体产品，工厂方法也具有唯一性，一般情况下，一个具体工厂中只有一个工厂方法或者一组重载的工厂方法。 **但是有时候我们需要一个工厂可以提供多个产品对象，而不是单一的产品对象**。

实现代码

```typescript
abstract class BMWFactory2 {
  abstract produce730BMW(): BMW730
  abstract produce840BMW(): BMW840
}

class ConcreteBMWFactory extends BMWFactory2 {
  produce730BMW(): BMW730 {
    return new BMW730()
  }

  produce840BMW(): BMW840 {
    return new BMW840()
  }
}

const bmwFactory = new ConcreteBMWFactory()

const bmw730_1 = bmwFactory.produce730BMW()
const bmw840_1 = bmwFactory.produce840BMW()

bmw730_1.run()
bmw840_1.run()
```

应用场景

- 一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节，这对于所有类型的工厂模式都是重要的。
- 系统中有多于一个的产品族，而每次只使用其中某一产品族。
- 系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于具体实现。

### 单例模式

单例模式（Singleton Pattern）是一种常用的模式，有一些对象我们往往只需要一个，比如全局缓存、浏览器中的 window 对象等。单例模式用于保证一个类仅有一个实例，并提供一个访问它的全局访问点。

实现代码

```typescript
class Singleton {
  // 定义私有的静态属性，来保存对象实例
  private static singleton: Singleton
  private constructor() {}

  // 提供一个静态的方法来获取对象实例
  public static getInstance(): Singleton {
    if (!Singleton.singleton) {
      Singleton.singleton = new Singleton()
    }
    return Singleton.singleton
  }
}
```

使用示例

```typescript
const instance1 = Singleton.getInstance()
const instance2 = Singleton.getInstance()

console.log(instance1 === instance2) // true
```

应用场景

- 需要频繁实例化然后销毁的对象。
- 创建对象时耗时过多或耗资源过多，但又经常用到的对象。
- 系统只需要一个实例对象，如系统要求提供一个唯一的序列号生成器或资源管理器，或者需要考虑资源消耗太大而只允许创建一个对象。

### 适配器模式

在实际生活中，也存在适配器的使用场景，比如：港式插头转换器、电源适配器和 USB 转接口。**而在软件工程中，适配器模式的作用是解决两个软件实体间的接口不兼容的问题**。 使用适配器模式之后，原本由于接口不兼容而不能工作的两个软件实体就可以一起工作。

实现代码

```typescript
interface Logger {
  info: (message: string) => Promise<void>
}

interface CloudLogger {
  sendToServer: (message: string, type: string) => Promise<void>
}

class AliLogger implements CloudLogger {
  public async sendToServer(message: string, type: string): Promise<void> {
    console.info(message)
    console.info('This Message was saved with AliLogger')
  }
}

class CloudLoggerAdapter implements Logger {
  protected cloudLogger: CloudLogger

  constructor(cloudLogger: CloudLogger) {
    this.cloudLogger = cloudLogger
  }

  public async info(message: string): Promise<void> {
    await this.cloudLogger.sendToServer(message, 'info')
  }
}
```

在以上代码中，因为 `Logger` 和 `CloudLogger` 这两个接口不匹配，所以我们引入了 `CloudLoggerAdapter` 适配器来解决兼容性问题。

使用示例

```typescript
(async () => {
  const aliLogger = new AliLogger()
  const cloudLoggerAdapter = new CloudLoggerAdapter(aliLogger)
  const notificationService = new NotificationService(cloudLoggerAdapter)
  await notificationService.send('Hello semlinker, To Cloud')
})()
```

应用场景及案例

- 以前开发的系统存在满足新系统功能需求的类，但其接口同新系统的接口不一致。
- 使用第三方提供的组件，但组件接口定义和自己要求的接口定义不同。
- [https://github.com/ctimmerm/axios-mock-adapter](https://github.com/ctimmerm/axios-mock-adapter)

### 观察者模式 & 发布订阅模式

#### 观察者模式

观察者模式，它定义了一种一对多的关系，让多个观察者对象同时监听某一个主题对象，这个主题对象的状态发生变化时就会通知所有的观察者对象，使得它们能够自动更新自己。

在观察者模式中有两个主要角色：`Subject`（主题）和 `Observer`（观察者）

实现代码

```typescript
interface Observer {
  notify: Function
}

class ConcreteObserver implements Observer {
  constructor(private name: string) {}

  notify() {
    console.log(`${this.name} has been notified.`)
  }
}

class Subject {
  private observers: Observer[] = []

  public addObserver(observer: Observer): void {
    console.log(observer, 'is pushed!')
    this.observers.push(observer)
  }

  public deleteObserver(observer: Observer): void {
    console.log('remove', observer)
    const n: number = this.observers.indexOf(observer)
    n != -1 && this.observers.splice(n, 1)
  }

  public notifyObservers(): void {
    console.log('notify all the observers', this.observers)
    this.observers.forEach(observer => observer.notify())
  }
}
```

使用示例

```typescript
const subject: Subject = new Subject()
const xiaoQin = new ConcreteObserver('小秦')
const xiaoWang = new ConcreteObserver('小王')
subject.addObserver(xiaoQin)
subject.addObserver(xiaoWang)
subject.notifyObservers()

subject.deleteObserver(xiaoQin)
subject.notifyObservers()
```

- 一个对象的行为依赖于另一个对象的状态。或者换一种说法，当被观察对象（目标对象）的状态发生改变时 ，会直接影响到观察对象的行为。
- [https://rxjs.dev/guide/subject](https://rxjs.dev/guide/subject)

#### 发布订阅模式

在软件架构中，发布/订阅是一种消息范式，**消息的发送者（称为发布者）不会将消息直接发送给特定的接收者（称为订阅者）。而是将发布的消息分为不同的类别，然后分别发送给不同的订阅者**。 同样的，订阅者可以表达对一个或多个类别的兴趣，只接收感兴趣的消息，无需了解哪些发布者存在。

在发布订阅模式中有三个主要角色：`Publisher`（发布者）、`Channels`（通道）和 `Subscriber`（订阅者）。

实现代码

```typescript
type EventHandler = (...args: any[]) => any

class EventEmitter {
  private c = new Map<string, EventHandler[]>()

  // 订阅指定的主题
  subscribe(type: string, ...handlers: EventHandler[]) {
    let topics = this.c.get(type)

    if (!topics) {
      this.c.set(type, (topics = []))
    }

    topics.push(...handlers)
  }

  // 为指定的主题发布消息
  publish(type: string, ...args: any[]): any[] | null {
    const topics = this.c.get(type)
    if (!topics) {
      return null
    }

    return topics.map((handler) => {
      try {
        return handler(...args)
      }
      catch (e) {
        console.error(e)
        return null
      }
    })
  }

  // 取消订阅指定的主题
  unsubscribe(topic: string, handler?: EventHandler): boolean {
    if (!handler) {
      return this.c.delete(topic)
    }

    const topics = this.c.get(topic)
    if (!topics) {
      return false
    }

    const index = topics.indexOf(handler)

    if (index < 0) {
      return false
    }
    topics.splice(index, 1)
    if (topics.length === 0) {
      this.c.delete(topic)
    }
    return true
  }
}
```

使用示例

```typescript
const eventEmitter = new EventEmitter()
eventEmitter.subscribe('ts', msg => console.log(`收到订阅的消息：${msg}`))

eventEmitter.publish('ts', 'TypeScript发布订阅模式')
eventEmitter.unsubscribe('ts')
eventEmitter.publish('ts', 'TypeScript发布订阅模式')
```

应用场景

- 对象间存在一对多关系，一个对象的状态发生改变会影响其他对象。
- 作为事件总线，来实现不同组件间或模块间的通信。
- [https://github.com/ustbhuangyi/better-scroll/blob/dev/packages/shared-utils/src/events.ts](https://github.com/ustbhuangyi/better-scroll/blob/dev/packages/shared-utils/src/events.ts)
- [https://mp.weixin.qq.com/s/N4iw3bi0bxJ57J8EAp5ctQ](https://mp.weixin.qq.com/s/N4iw3bi0bxJ57J8EAp5ctQ)

### 策略模式

策略模式（Strategy Pattern）定义了一系列的算法，把它们一个个封装起来，并且使它们可以互相替换。策略模式的重心不是如何实现算法，而是如何组织、调用这些算法，从而让程序结构更灵活、可维护、可扩展。

实现代码

```typescript
interface Strategy {
  authenticate: (...args: any) => any
}

class Authenticator {
  strategy: any
  constructor() {
    this.strategy = null
  }

  setStrategy(strategy: any) {
    this.strategy = strategy
  }

  authenticate(...args: any) {
    if (!this.strategy) {
      console.log('尚未设置认证策略')
      return
    }
    return this.strategy.authenticate(...args)
  }
}

class WechatStrategy implements Strategy {
  authenticate(wechatToken: string) {
    if (wechatToken !== '123') {
      console.log('无效的微信用户')
      return
    }
    console.log('微信认证成功')
  }
}

class LocalStrategy implements Strategy {
  authenticate(username: string, password: string) {
    if (username !== 'abao' && password !== '123') {
      console.log('账号或密码错误')
      return
    }
    console.log('账号和密码认证成功')
  }
}
```

使用示例

```typescript
const auth = new Authenticator()

auth.setStrategy(new WechatStrategy())
auth.authenticate('123456')

auth.setStrategy(new LocalStrategy())
auth.authenticate('abao', '123')
```

- 一个系统需要动态地在几种算法中选择一种时，可将每个算法封装到策略类中。
- 多个类只区别在表现行为不同，可以使用策略模式，在运行时动态选择具体要执行的行为。
- 一个类定义了多种行为，并且这些行为在这个类的操作中以多个条件语句的形式出现，可将每个条件分支移入它们各自的策略类中以代替这些条件语句。
- [https://github.com/jaredhanson/passport-local](https://github.com/jaredhanson/passport-local)
- [https://github.com/jaredhanson/passport-oauth2](https://github.com/jaredhanson/passport-oauth2)

### 职责链模式

职责链模式是使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间的耦合关系。在职责链模式里，很多对象由每一个对象对其下家的引用而连接起来形成一条链。请求在这个链上传递，直到链上的某一个对象决定处理此请求。

实现代码

```typescript
interface IHandler {
  addMiddleware: (h: IHandler) => IHandler
  get: (url: string, callback: (data: any) => void) => void
}

abstract class AbstractHandler implements IHandler {
  next!: IHandler
  addMiddleware(h: IHandler) {
    this.next = h
    return this.next
  }

  get(url: string, callback: (data: any) => void) {
    if (this.next) {
      return this.next.get(url, callback)
    }
  }
}

// 定义Auth中间件
class Auth extends AbstractHandler {
  isAuthenticated: boolean
  constructor(username: string, password: string) {
    super()

    this.isAuthenticated = false
    if (username === 'abao' && password === '123') {
      this.isAuthenticated = true
    }
  }

  get(url: string, callback: (data: any) => void) {
    if (this.isAuthenticated) {
      return super.get(url, callback)
    }
    else {
      throw new Error('Not Authorized')
    }
  }
}

// 定义Loger中间件
class Loger extends AbstractHandler {
  get(url: string, callback: (data: any) => void) {
    console.log('/GET Request to: ', url)
    return super.get(url, callback)
  }
}

class Route extends AbstractHandler {
  URLMaps: { [key: string]: any }
  constructor() {
    super()
    this.URLMaps = {
      '/api/todos': [{ title: 'learn ts' }, { title: 'learn react' }],
      '/api/random': Math.random(),
    }
  }

  get(url: string, callback: (data: any) => void) {
    super.get(url, callback)

    if (this.URLMaps.hasOwnProperty(url)) {
      callback(this.URLMaps[url])
    }
  }
}
```

使用示例

```typescript
const route = new Route()
route.addMiddleware(new Auth('abao', '123')).addMiddleware(new Loger())

route.get('/api/todos', (data) => {
  console.log(JSON.stringify({ data }, null, 2))
})

route.get('/api/random', (data) => {
  console.log(data)
})
```

应用场景

- 可处理一个请求的对象集合应被动态指定。
- 想在不明确指定接收者的情况下，向多个对象中的一个提交一个请求。
- 有多个对象可以处理一个请求，哪个对象处理该请求运行时自动确定，客户端只需要把请求提交到链上即可。

### 模板方法模式

模板方法模式由两部分结构组成：抽象父类和具体的实现子类。**通常在抽象父类中封装了子类的算法框架，也包括实现一些公共方法以及封装子类中所有方法的执行顺序**。子类通过继承这个抽象类，也继承了整个算法结构，并且可以选择重写父类的方法。

实现代码

```typescript
import fs from 'node:fs'

abstract class DataParser {
  data: string = ''
  out: any = null

  // 这就是所谓的模板方法
  parse(pathUrl: string) {
    this.readFile(pathUrl)
    this.doParsing()
    this.printData()
  }

  readFile(pathUrl: string) {
    this.data = fs.readFileSync(pathUrl, 'utf8')
  }

  abstract doParsing(): void

  printData() {
    console.log(this.out)
  }
}

class CSVParser extends DataParser {
  doParsing() {
    this.out = this.data.split(',')
  }
}

class MarkupParser extends DataParser {
  doParsing() {
    this.out = this.data.match(/<\w+>.*<\/\w+>/g)
  }
}
```

使用示例

```typescript
const csvPath = './data.csv'
const mdPath = './design-pattern.md'

new CSVParser().parse(csvPath)
new MarkupParser().parse(mdPath)
```

应用场景

- 算法的整体步骤很固定，但其中个别部分易变时，这时候可以使用模板方法模式，将容易变的部分抽象出来，供子类实现。
- 当需要控制子类的扩展时，模板方法只在特定点调用钩子操作，这样就只允许在这些点进行扩展
