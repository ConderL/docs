---
title: class
---

## class 基础
### 声明

MDN:

>class 声明创建一个基于原型继承的具有给定名称的新类。

- 同function一样,class声明方式也具有类声明及类表达式声明
- 不同的是类声明不能提升, 并且不能重复声明。
```javascript
console.log(Person) // Uncaught ReferenceError: Cannot access 'Person' before initialization
class Person {} // 类声明
const Personal = class {} // 类表达式声明
```

### 构造方法
`constructor` 是专门用来创建和初始化对象的方法。
```javascript
class Person {
  sex = '男' // 直接在外部定义属性相当于constructor内部使用this.sex = '男', 两者都为public属性;
  constructor(...args) {
    // args为参数，不支持arguments
    const [name, age] = args
    this.name = name
    this.age = age
    this.smile = function () {} // smile 是挂载到class对象上的方法
  }

  speak() {} // speak是挂载到class对象原型上的方法并默认设置为不可枚举
}
new Person('张三', 18)
```

### static
MDN:

>类（class）通过 static 关键字定义静态方法。不能在类的实例上调用静态方法，而应该通过类本身调用。这些通常是实用程序方法，例如创建或克隆对象的功能。
意思就是挂载到类本身上，而不是类构造出来的实例对象上。

```javascript
class Person {
  static type = 'advanced Animal'
  static think() {}
}
Person.type // 属性及方法都需要通过类名访问
Person.think()
```

### get/set修饰符

同Object一样，使用get/set可以对访问属性进行拦截(实例对象的访问)。
```javascript
class Person {
  sex = '男' // 这里定义的属性则不支持get/set拦截
  get name() {
    return '张三'
  }

  set name(value) { // 对修改name的值进行拦截，变为更改alias
    this.alias = value
  }
}
const person = new Person()
person.name // 张三
person.name = '李四' // 赋值后 person.name仍是张三
person.alias // 李四
```

### super

super只能在类和对象的方法中用。
对象的方法中通过super找到父类。
类中super方法相当于借用构造函数继承。
- super() 指调用父类的构造函数
- 必须在 constructor 函数里的this 调用前执行 super()

```javascript
class Father {
  constructor(name) {
    this.name = name
  }

  show() {
    console.log(this.name)
  }
}
class Person extends Father {
  constructor(name) {
    super(name)
  }
}
const hd = new Person('张三')
hd.show()
```

### instanceof

`instanceof`原理就是判断前者的原型链上是否有后者的原型。

```javascript
Object.getPrototypeOf([]) === Array.prototype // true;
function checkPrototype(obj, constructor) {
  if (!obj.__proto__)
    return false
  if (obj.__proto__ == constructor.prototype)
    return true
  return checkPrototype(obj.__proto__, constructor)
}
```

### new 运算符

 `new` 运算符原理：
 1. 创建一个原型为构造函数原型的空对象
 2. 把构造函数的this指向这个空对象，并传参
 3. 判断构造函数返回类型，如果是对象，则返回这个对象，反之则返回第一步创建的对象。

```javascript
function Person(name) {
  this.name = name
}
Person.prototype.firstName = '彭于晏'
function myNew(construc, ...args) {
  const obj = Object.create(construc.prototype)
  const result = construc.apply(obj, args)
  if (result instanceof Object) {
    return result
  }
  else {
    return obj
  }
}
const person = myNew(Person, '远哥')
```
