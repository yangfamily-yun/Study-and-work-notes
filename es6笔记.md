for...in和for...of的区别

前者保存键名，后者保存的是键值

[TOC]



# 第一章  es6新特性

## 1、let关键字

`let` 关键字用来声明变量，使用 `let` 声明的变量有几个特点：

- 不允许重复声明
- 块级作用域
- 不存在变量提升
- 不影响作用域链

应用场景：以后声明变量使用let 就对了

**案例1**：给多个 `div` 循环注册点击事件

- 错误示例:

  ```
  // 错误示例，divs.length === 3
          //遍历并绑定事件
          for(let i = 0;i<items.length;i++){
              items[i].onclick = function(){
                  //修改当前元素的背景颜色
                  // this.style.background = 'pink';
                  items[i].style.background = 'pink';
                   console.log(i); // 3
              }
          }
  //for循环在页面加载完成后立即执行，而回调函数是在事件触发后执行，函数作用域没有i，向上一级作用域（全局作用域）寻找，此时i为3
  //定时器模块在分线程，当时机到了，回调函数会进入主线程中的队列，当初始代码全部执行完毕，就会循环遍历队列的回调函数执行
  ```

  解决办法：（1）将以上代码中的 `var` 改为 `let`，上一级作用域变成块级作用域，每个 i都会形成一个自己的块级作用域（2）事件嵌套于匿名自调用函数中，形成闭包，每个回调函数都会有自己的闭包，延长局部变量的生存周期

**案例2**：1s 后循环输出所有数字

- 错误示例：

  ```js
  for (var i = 1; i <= 5; i++) {
      setTimeout(() => {
          console.log(i);
      }, 1000);
  }
  /*
  输出：6 6 6 6 6
  循环从1-5的时间很短暂，远不及 1s。
  此时五个异步事件瞬间加入到异步事件队列中，等待 1s后依次执行。
  而此时i为6，故瞬间输出 5 个 6。
  异步事件队头
  (1) console.log(i);
  (2) console.log(i);
  (3) console.log(i);
  (4) console.log(i);
  (5) console.log(i);
  */Copy to clipboardErrorCopied
  ```

- 正确示例：

  ```js
  for (let j = 1; j <= 5; j++) {
      setTimeout(() => {
          console.log(j);
      }, 1000);
  }
  // 输出：1 2 3 4 5
  // let 有块级作用域，每个 j 都会形成一个自己的块级作用域，与相应的异步事件共享：
  // {j = 1;} {j = 2;} {j = 3;} {j = 4;} {j = 5;}Copy to clipboardErrorCopied
  ```

- 解决方法2：

  ```js
  // 给每一个 i 设置一个立即执行函数，会形成自己的块级作用域，不影响外部变量。
  for (var i = 1; i <= 5; i++) {
      (function (i) {
          setTimeout(() => {
              console.log(i);
          }, 1000);
      })(i);
  ```

## 2、const关键字

`const` 关键字用来声明常量，`const` 声明有以下特点：

- 声明必须赋初始值
- 标识符一般为大写
- 不允许重复声明
- 值不允许修改
- 块级作用域

注意：修改对象、数组中的元素不会报错，因为变量保存的地址值没有发生改变
**应用场景**：声明对象类型使用 const，非对象类型声明选择 let

## 3. 变量的解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为 **解构赋值**。

数组的解构赋值：

```js
const arr = ['red', 'green', 'blue'];
let [r, g, b] = arr;
```

对象的解构赋值：

```js
const obj = {
    uname: 'rick',
    age: 30,
    sayHi: function () {
        console.log('hello');
    },
    sayBye() {
        console.log('Bye~');
    }
}
let {name, age, sayHi} = obj;
let {sayBye} = obj;
```

**注意：对象的解构赋值，变量名必须和对象的属性名相同，想得到对象的哪个属性，解构赋值时就写哪个属性名名，可以只写需要的属性名**

**应用场景**：频繁使用对象方法、数组元素，就可以使用解构赋值形式。

## 4. 模板字符串

模板字符串（template string）是增强版的字符串，用反引号 ` 标识，特点：

- 字符串内容中可以出现换行符
- 可以使用 `${变量名}` 形式输出变量值，拼接内容

```js
let name = 'jack';
console.log(`hello, ${name}`);
let ul = `<ul>
    <li>apple</li>
    <li>banana</li>
    <li>peach</li>
    </ul>`
```

应用场景：当遇到字符串与变量拼接的情况使用模板字符串。

## 5. 简化对象写法

ES6 允许在大括号里面，直接写入变量名和函数名，作为对象的属性和方法。这样的书写更加简洁。

```js
        let name = '尚硅谷';
        let change = function(){
            console.log('我们可以改变你!!');
        }

        const school = {
            //如果变量名在祖先作用域中没有声明并且赋值，则属性值为空串
            name,
            change,
            //简化：function
            improve(){
                console.log("我们可以提高你的技能");
            }
        }

        console.log(school);
```

**应用场景**：对象简写形式简化了代码，所以以后用简写就对了。

## 6. 箭头函数

ES6 允许使用「箭头」`=>` 定义函数。

- ```
  function
  ```

  写法：

  ```js
  function fn(param1, param2, …, paramN) { 
      // 函数体
      return expression; 
  }Copy to clipboardErrorCopied
  ```

- ```
  =>
  ```

  写法：

  ```js
  let fn = (param1, param2, …, paramN) => {
      // 函数体
      return expression;
  }Copy to clipboardErrorCopied
  ```

箭头函数的 **注意点**:

- 箭头函数 `this` 始终指向声明时所在作用域下 `this` 的值
- 箭头函数不能作为构造函数实例化
- 不能使用 `arguments`
- 箭头函数的简写
  - 省略小括号,如果形参只有一个，则小括号可以省略
  - 省略花括号, 当代码体只有一条语句的时候, 此时 return 必须省略,函数的返回值为该条语句的执行结果

案例1：箭头函数 `this` **始终**指向声明时所在作用域下 `this` 的值，`call` 等方法无法改变其指向。

```js
let obj = {
    uname: 'rick',
    age: 30
};
let foo = () => {
    console.log(this);
}
let bar = function () {
    console.log(this);
}
// call 对箭头函数无效
foo.call(obj); // window
bar.call(obj); // obj {...}
```

案例2：筛选偶数

```js
let arr = [2, 4, 5, 10, 12, 13, 20];
let res = arr.filter(v => v % 2 === 0);
console.log(res); // [2, ,4 10, 12, 20]
```

案例3：点击 div两秒后变成粉色

- 方案1：使用 `_this` 保存 `div` 下的 `this`，从而设置 `div` 的 `style` 属性。闭包

  ```js
  div.addEventListener('click', function () {
      let _this = this;
      setTimeout(function () {
          //定时器的回调函数的this始终指向window
          console.log(_this); // <div>...<div>
          _this.style.backgroundColor = 'pink';
      }, 2000)
  });
  ```

- 方案2：使用 `=>` 箭头函数

  ```js
  div.addEventListener('click', function () {
      setTimeout(() => {
          console.log(thid); // <div>...</div>
          this.style.backgroundColor = 'pink';
      }, 2000);
  });
  ```

 **适用场景**：箭头函数适合与 this 无关的回调. 定时器, 数组的方法回调，箭头函数不适合与 this 有关的回调.  事件回调, 对象的方法

## 7. 函数参数默认值设定

ES6 允许给函数参数设置默认值，当调用函数时不给实参，则使用参数默认值。

特点：

1、具有默认值的形参，一般要靠后，不然没有意义

```js
let add = (x, y, z=3) => x + y + z;
console.log(add(1, 2)); // 6
```

2、可与**解构赋值**结合，如果实参的属性没有赋值，则会使用形参中的值

```js
function connect({ host = '127.0.0.1', uesername, password, port }) {
    console.log(host); // 127.0.0.1
    console.log(uesername);
    console.log(password);
    console.log(port);
}
connect({
    // host: 'docs.mphy.top',
    uesername: 'root',
    password: 'root',
    port: 3306
})
```

## 8. rest 参数

ES6 引入 rest 参数，用于获取函数的实参，用来代替 `arguments`，作用与 `arguments` 类似。

**与arguments不同**：arguments是一个类数组对象，可以使用length和索引操作元素，但不能用数组方法。而rest是数组，可以使用数组方法操作元素（filter some every map）

用在函数形参中，语法格式：`fn(a, b, ...args)`，**写在参数列表最后面**，其他位置会报错

```js
let fn = (a, b, ...args) => {
    console.log(a);
    console.log(b);
    console.log(args);
};
fn(1,2,3,4,5);
/*
1
2
[3, 4, 5]
*/
```

案例1：求不定个数数字的和

```js
let add = (...args) => {
    let sum = args.reduce((pre, cur) => pre + cur, 0);
    return sum;
}
console.log(add(1, 2, 3, 4, 5)); // 15Copy to clipboardErrorCopied
```

应用场景：rest 参数非常适合不定个数参数函数的场景

## 9. spread 扩展运算符

扩展运算符也是三个点（`...`）。不同的是它在实参中，而rest参数在形参中。它好比 rest 参数的逆运算，**将一个数组转为用逗号分隔的参数序列，对数组进行解包**。可用在调用函数时，传递的实参，将一个数组转换为参数序列。

扩展运算符也可以将对象解包。

展开数组：

```js
function fn(a, b, c) {
    console.log(arguments);
    console.log(a + b + c);
}
let arr = ['red', 'green', 'blue']; 
fn(...arr)
// [Arguments] { '0': 'red', '1': 'green', '2': 'blue' }伪数组对象有三个属性，如果不用扩展运算符，他就只有一个属性（数组）
```

案例1：数组合并

```js
let A = [1, 2, 3];
let B = [4, 5, 6];
let C = [...A, ...B];
console.log(C); // [1, 2, 3, 4, 5, 6]
```

案例2：数组克隆
如果数组中的元素是引用数据类型，他会进行浅拷贝

```js
let arr1 = ['a', 'b', 'c'];
let arr2 = [...arr1];
console.log(arr2); // ['a', 'b', 'c']
```

案例3：将伪数组转换为真实数组

```js
const divs = document.querySelectorAll('div');//伪数组对象
let divArr = [...divs];
console.log(divArr);//数组
```

案例4：对象合并

```js
// 合并对象
let obj1 = {
    a: 123
};
let obj2 = {
    b: 456
};
let obj3 = {
    c: 789
};
let obj = { ...obj1, ...obj2, ...obj3 };
console.log(obj);
// { a: 123, b: 456, c: 789 }
```

## 10. Symbol

### 10.1 Symbol 基本介绍与使用

ES6 引入了一种新的原始数据类型 `Symbol`，表示**独一无二的值**。它是 JavaScript 语言的第七种数据类型，是一种类似于字符串的数据类型。

JavaScript 的七种基本数据类型：

- 值类型（基本类型）：string、number、boolean、undefined、null、symbol
- 引用数据类型：object（包括了array、function）

Symbol 的特点：

- Symbol 的值是唯一的，用来解决命名冲突的问题
- Symbol 值不能与其他数据进行运算
- Symbol 定义的对象属性不能使用 `for...in` 循环遍历，但是可以使用 `Reflect.ownKeys` 来获取对象的所有键名
- typeof检查变量返回'symbol'
- 直接打印变量返回Symbol(“描述”);

Symbol 的创建：

- 创建一个Symbol函数直接调用

  ```js
  let s1 = Symbol();
  console.log(s1, typeof s1);//'symbol'
  // Symbol() 
  ```

- 添加具有标识的Symbol(实参)

  创建实参相同的，它们值都是唯一的，不同

  ```js
  let s2 = Symbol('1');
  let s2_1 = Symbol('1');
  console.log(s2 === s2_1); 
  // false
  // Symbol表示的值 都是独一无二的
  ```

- 使用Symbol.for()

  方法创建，实参相同的，它们具有相同的实体，它们的值是一样的。

  ```js
  let s3 = Symbol.for('apple');
  let s3_1 = Symbol.for('apple');
  console.log(s3 === s3_1); // true
  ```

- 输出Symbol，变量的描述，使用**description**属性

  ```js
  let s4 = Symbol('测试');
  console.log(s4.description); // 测试
  ```

### 10.2 对象添加 Symbol 类型的属性

案例1：向对象中添加唯一的属性和方法。
分析：如果直接向对象中添加属性或方法，则原来对象中可能已经存在了同名属性或方法，会覆盖掉原来的。所以使用 `Symbol` 生成唯一的属性或方法名，可以更加安全的添加。

属性或方法的创建：

- 数组，解构赋值
- 对象属性
- symbol

代码实现：

```js
// 这是一个 game 对象，假设我们不知道里面有什么属性和方法
const game = {
    uname: '俄罗斯方块',
    up: function () { },
    down: function () { }
}
//声明数组
// 通过 Symbol 生成唯一的属性名，然后给 game 添加方法
let [up, down] = [Symbol('up'), Symbol('down')];
  //声明一个对象
        // let methods = {
        //     up: Symbol(),
        //     down: Symbol()
        // };

        // game[methods.up] = function(){
        //     console.log("我可以改变形状");
        // }

        // game[methods.down] = function(){
        //     console.log("我可以快速下降!!");
        // }
//symbol属性
/* let youxi = {
            name:"狼人杀",
            [Symbol('say')]: function(){
                console.log("我可以发言")
            },
            [Symbol('zibao')]: function(){
                console.log('我可以自爆');
            }
        }

        console.log(youxi)
        */
game[up] = function () {
    console.log('up');
}
game[down] = function () {
    console.log('down');
}

// 调用刚刚创建的方法
game[up]();
game[down]();
```

### 10.3 Symbol 内置值

除了定义自己使用的 Symbol 值以外，ES6 还提供了11 个内置的 Symbol 值，指向语言内部使用的方法。可以称这些方法为魔术方法，因为它们会在特定的场景下自动执行。

| 方法                         | 描述                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| `Symbol.hasInstance`         | 当其他对象使用 `instanceof` 运算符，判断是否为该对象的实例时，会调用这个方法 |
| `Symbol.isConcatSpreadable ` | 对象的 `Symbol.isConcatSpreadable` 属性等于的是一个布尔值，表示该对象用于`Array.prototype.concat()` 时，是否可以展开 |
| `Symbol.species`             | 创建衍生对象时，会使用该属性                                 |
| `Symbol.match`               | 当执行 `str.match(myObject)` 时，如果该属性存在，会调用它，返回该方法的返回值。 |
| `Symbol.replace `            | 当该对象被 `str.replace(myObject)` 方法调用时，会返回该方法的返回值。 |
| `Symbol.search `             | 当该对象被 `str.search(myObject)` 方法调用时，会返回该方法的返回值。 |
| `Symbol.split `              | 当该对象被 `str.split(myObject)` 方法调用时，会返回该方法的返回值。 |
| `Symbol.iterator `           | 对象进行` for...of` 循环时，会调用 `Symbol.iterator` 方法，返回该对象的默认遍历器 |
| `Symbol.toPrimitive `        | 该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。 |
| `Symbol. toStringTag `       | 在该对象上面调用 `toString()` 方法时，返回该方法的返回值     |
| `Symbol. unscopables `       | 该对象指定了使用 `with` 关键字时，哪些属性会被 `with` 环境排除。 |

案例1：`Symbol.hasInstance` 方法判断是否属于这个对象时被调用。

```js
class A {
    static [Symbol.hasInstance]() {
        console.log('判断是否属于这个对象时被调用');
    }
}
let obj = {};
console.log(obj instanceof A
// 判断是否属于这个对象时被调用
// false
```

案例2：数组使用 `concat` 方法时，是否可以展开。

```js
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
let arr3 = [4, 5, 6];
arr2[Symbol.isConcatSpreadable] = false;
console.log(arr1.concat(arr2));
// [ 1, 2, 3, [ 4, 5, 6, [Symbol(Symbol.isConcatSpreadable)]: false ] ]
console.log(arr1.concat(arr3));
// [ 1, 2, 3, 4, 5, 6 ]
```

## 11. 迭代器

### 11.1 定义

遍历器（`Iterator`）就是一种机制。它是一种接口（对象的属性，它对应一个方法），为各种不同的数据结构（对象，数组等）提供统一的访问机制。任何数据结构只要部署 `Iterator` 接口，就可以使用for...of遍历操作，没有`Iterator` 接口就不可以用`for...of` 循环遍历。

- ES6 创造了一种新的遍历命令 `for...of` 循环，`Iterator` 接口主要供 `for...of` 消费。
- 原生具备iterator接口的数据(可用for of 遍历)
  - `Array`
  - `Arguments`
  - `Set`
  - `Map`
  - `String`
  - `TypedArray`
  - `NodeList`

案例：使用 `next()` 方法遍历原生自带 `iterator` 接口的数据：

```js
// 遍历 Map
const mp = new Map();
mp.set('a', 1);
mp.set('b', 2);
mp.set('c', 3);
//创建一个指针对象，指向当前数据结构的起始位置
let iter1 = mp[Symbol.iterator]();
// next() 方法每执行一次，指针自增
//第一次调用对象的 next 方法，指针自动指向数据结构的第一个成员
//接下来不断调用 next 方法，指针一直往后移动，直到指向最后一个成员
//每调用 next 方法返回一个包含 value 和 done 属性的对象
console.log(iter1.next()); // { value: [ 'a', 1 ], done: false }
console.log(iter1.next()); // { value: [ 'b', 2 ], done: false }
console.log(iter1.next()); // { value: [ 'c', 3 ], done: false }
console.log(iter1.next()); // { value: undefined, done: true }
// 遍历数组
let xiyou = ['唐僧','孙悟空','猪八戒','沙僧'];
let iter2 = xiyou[Symbol.iterator]();
console.log(iter2.next()); // { value: '唐僧', done: false }
console.log(iter2.next()); // { value: '孙悟空', done: false }
console.log(iter2.next()); // { value: '猪八戒', done: false }
console.log(iter2.next()); // { value: '沙僧', done: false }
```

上面的案例只是为了证明他们自带 `iterator` 接口，实际上直接使用 `for...of` 方法遍历即可（`iterator` 接口为 `for...of`）服务。例如，可以使用 `for [k, v] of map` 来遍历 Map 数据结构中的键和值。

```js
const mp = new Map();
mp.set('a', 1);
mp.set('b', 2);
mp.set('c', 3);
for (let [k, v] of mp) {
    console.log(k, v);
}
/*
a 1
b 2
c 3
*/
```

### 11.2 工作原理

- 使用`Iterator` 接口创建一个指针对象，指向当前数据结构的起始位置
- 第一次调用指针对象的 `next` 方法，指针自动指向数据结构的第一个成员
- 接下来不断调用 `next` 方法，指针一直往后移动，直到指向最后一个成员
- 每调用 `next` 方法返回一个包含 `value` 和 `done` 属性的对象

**应用场景**：需要自定义遍历数据的时候，要想到迭代器。

### 11.3 自定义遍历数据

我们可以通过给数据结构添加自定义 `[Symbol.iterator]()` 方法来使该数据结构能够直接被遍历，从而使 `for...of` 能够直接遍历指定数据，达到为 `for...of` 服务的功能。

```js
// 需求：遍历对象的数组属性
const xiaomi = {
    uname: '小明',
    course: [ '高数', '大物', '英语', '数据库' ],
    // 通过自定义 [Symbol.iterator]() 方法
    [Symbol.iterator]() {
        // 初始指针对象指向数组第一个
        let index = 0;
        // 保存 xiaomi 的 this 值
        let _this = this;
        return {
            next: function () {
                // 不断调用 next 方法，直到指向最后一个成员
                if (index < _this.course.length) {
                    return { value: _this.course[index++], done: false };
                } else {
                    // 每调用next 方法返回一个包含value 和done 属性的对象
                    return { value: undefined, done: true };
                }
            }
        }
    }
}
// for...of直接遍历达到目的
for (let v of xiaomi) {
    console.log(v);
}
```

## 12. Generator 生成器函数

### 12.1 生成器函数的声明和调用

生成器函数是 ES6 提供的一种 **异步编程解决方案**，语法行为与传统函数完全不同。

- 使用 ` * `  可以声明一个生成器函数，`*` 的位置没有限制
- 执行生成器函数得到迭代器对象，调用迭代器对象的 `next` 方法控制生成器函数体的执行（而传统函数调用后会直接执行函数体），**同时**会将 `yield` 语句后的值以包含 `value` 和 `done` 属性的对象返回。
- 每一个 `yield` 相当于**函数的暂停标记**，也可以认为是一个函数代码的分隔符，每调用一次 `next()`，生成器函数就往下执行一段。
- `next` 方法可以传递实参，作为 上一条`yield` 语句的返回值

例如以下生成器函数中，3 个 `yield` 语句将函数内部分成了 4 段。

```js
function* generator() {
    console.log('before 111');
    console.log("shisysihs");
    yield 111;// 生成器第 1 段
    console.log('before 222'); 
    yield 222;// 生成器第 2 段
    console.log('before 333'); 
    yield 333;// 生成器第 3 段
    console.log('after 333'); 
}
let iter = generator();
console.log(iter.next());
console.log(iter.next());
console.log(iter.next());
console.log(iter.next());
/*
before 111 shisysihs
{ value: 111, done: false }
before 222
{ value: 222, done: false }
before 333
{ value: 333, done: false }
after 333
{ value: undefined, done: true }
*/
```

### 12.2 生成器函数的参数传递

迭代器对象的next方法可以传入实参，它将作为上一条yield语句的返回值，从而可以在函数内部拿到外部的数据进行操作

```js
function* generator(arg) {
    console.log(arg); 
    let one = yield 111;// 生成器第 1 段
    console.log(one); //bbb
    let two = yield 222;// 生成器第 2 段
    console.log(two); ///ccc
    let three = yield 333; // 生成器第 3 段
    console.log(three); // ddd生成器第 4 段
}

let iter = generator('aaa'); // 
console.log(iter.next());
//next方法可以传入实参
console.log(iter.next('bbb')); // 传给生成器yield第 2 段，实参为上一次yield语句的返回值
console.log(iter.next('ccc')); // 传给生成器第yield 3 段，实参为上一次yield语句的返回值
console.log(iter.next('ddd')); // 传给生成器第yield 4 段，实参为上一次yield语句的返回值
/*
aaa
{ value: 111, done: false }
bbb
{ value: 222, done: false }
ccc
{ value: 333, done: false }
ddd
{ value: undefined, done: true }
*
```

### 12.3 生成器函数案例

案例1：1s后输出111，2s后输出222，3s后输出333

- 传统方式：嵌套太多，代码复杂，产生 **回调地狱**（回调里面嵌套回调）。

  ```js
  setTimeout(() => {
      console.log(111);
      setTimeout(() => {
          console.log(222);
          setTimeout(() => {
              console.log(333);
          }, 3000);
      }, 2000);
  }, 1000);Copy to clipboardErrorCopied
  ```

- 生成器实现：结构简洁明了

  ```js
  function one() {
      setTimeout(() => {
          console.log(111);
          iter.next();
      }, 1000);
  }
  
  function two() {
      setTimeout(() => {
          console.log(222);
          iter.next();
      }, 2000);
  }
  
  function three() {
      setTimeout(() => {
          console.log(333);
      }, 3000);
  }
  
  function* generator() {
      yield one();
      yield two();
      yield three();
  }
  
  let iter = generator();
  iter.next();
  ```

案例2：生成器函数模拟每隔1s获取商品数据

```js
function getUsers() {
    setTimeout(() => {
        let data = '用户数据';
        iter.next(data); // 传参给生成器函数的第 2 段，后面类似
    }, 1000);
}

function getOrders() {
    setTimeout(() => {
        let data = '订单数据';
        iter.next(data);
    }, 1000);
}

function getGoods() {
    setTimeout(() => {
        let data = '商品数据';
        iter.next(data);
    }, 1000);
}

function* generator() {
    let users = yield getUsers();
    console.log(users);
    let orders = yield getOrders();
    console.log(orders);
    let goods = yield getGoods();
    console.log(goods);
}

let iter = generator();
iter.next();
```

## 13. Promise

### 13.1 Promise 的定义和使用

`Promise` 是 ES6 引入的**异步编程的新解决方案**。语法上 Promise 是一个构造函数，调用时传入一个函数作为参数，用来封装异步操作并可以获取其成功或失败的结果（resolve, reject修改promise对象的状态）。

一个 `Promise` 必然处于以下几种状态之一：

- 待定（`pending`）：初始状态，既没有被兑现，也没有被拒绝。
- 已兑现（`fulfilled`）：意味着操作成功完成。
- 已拒绝（`rejected`）：意味着操作失败。

`Promise` 的使用：

- Promise 构造函数：`new Promise((resolve, reject)=>{封装定时器、文件操作、数据库操作、数据请求等异步操作得到数据，数据作为参数调用resolve, reject修改promise对象的状态，这是结束，而数据的处理在promise对象的then方法中})`
- `Promise.prototype.then` 方法，两个函数作为参数，当promise状态为成功时，调用第一个函数，否则调用第二个函数，在这进行数据的处理
- `Promise.prototype.catch` 方法

一个简单案例：

```js
let p = new Promise(function (resolve, reject) {
    // 使用 setTimeout 模拟请求数据库数据操作
    setTimeout(function () {
        // 这个异步请求数据库数据操作是否正确返回数据
        let isRight = true;
        if (isRight) {
            let data = '数据库中的数据';
            // 设置 Promise 对象的状态为操作成功
            resolve(data);
        } else {
            let err = '数据读取失败！'
            // 设置 Promise 对象的状态为操作失败
            reject(err);
        }
    }, 1000);
});
p.then(function (value) {
    console.log(value);
}, function (reason) {
    console.error(reason);
})
```

### 13.2 Promise 封装读取文件

```js
// 使用 nodejs 的 fs 读取文件模块
const fs = require('fs');

const p = new Promise(function (resolve, reject) {
    fs.readFile('./resources/为学.txt', (err, data) => {
        // err 是一个异常对象
        if (err) reject(err);
        resolve(data);
    })
})

p.then(function (value) {
    // 转为字符串输出
    console.log(value.toString());
}, function (reason) {
    console.log('读取失败!!');
})
```

### 13.3 Promise 封装 Ajax 请求

```js
const p = new Promise((resolve, reject) => {
    //1. 创建对象
    const xhr = new XMLHttpRequest();
    //2、初始化
    xhr.open('get', 'https://api.apiopen.top/getJoke');
    //3、发送对象
    xhr.send();
    xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
            if (xhr.status >= 200 && xhr.status < 300) {
                // 成功
                resolve(xhr.response);
            } else {
                // 失败
                reject(xhr.status);
            }
        }
    }
});

// 指定回调
p.then(function (value) {
    console.log(value);
}, function (reason) {
    console.error(reason);
})
```

### 13.4 Promise.prototype.then 方法

先复习一下一个 `Promise` 的三种状态：

- 待定（`pending`）：初始状态，既没有被兑现，也没有被拒绝。
- 已兑现（`fulfilled`）：意味着操作成功完成。
- 已拒绝（`rejected`）：意味着操作失败。

`Promise.prototype.then` 方法返回的结果依然是 `Promise` 对象，**对象状态由回调函数的执行结果决定**。

具体情况如下：

- 若then方法没有返回值，则then方法返回的对象的状态值为成功fulfilled，值为undefined。

  ```js
  const p = new Promise((resolve, reject) => {
      setTimeout(() => {
          // resolve('用户数据')
          reject('出错了');
      }, 1000);
  })
  // 未设定返回值
  const res = p.then((value) => {
      console.log(value);
  }, (reason) => {
      console.warn(reason);
  })
  // 打印 then 方法的返回值
  console.log(res);
  ```

  打印的结果：

   ![es6-3](https://cdn.jsdelivr.net/gh/Hacker-C/Picture-Bed@main/docs/es6-3.504usmc4kes0.png)

- 如果回调函数中返回的结果是非 Promise类型的属性，则then方法返回的成功（fulfilled）状态的promise对象，不管then方法执行的是哪一个回调，其状态（fulfilled），值取决于then方法所执行的函数的返回值

  ```js
  const p = new Promise((resolve, reject) => {
      setTimeout(() => {
          // resolve('用户数据')
          reject('出错了');
      }, 1000);
  })
   // 返回的非 Promise 对象
  const res = p.then((value) => {
      console.log(value);
      return '成功了！！';
  }, (reason) => {
      console.warn(reason);
      return '出错啦！！'
  })
  // 打印 then 方法的返回值
  console.log(res);
  ```

  打印结果：

   ![es6-4](https://cdn.jsdelivr.net/gh/Hacker-C/Picture-Bed@main/docs/es6-4.2xdcb1x8jya0.png)

- 如果回调函数中返回的结果是Promise类型（return new Promise()），则then 方法返回的Promise对象状态与该返回结果的状态相同，返回值也相同。

  ```js
  const p = new Promise((resolve, reject) => {
      setTimeout(() => {
          resolve('用户数据')
          // reject('出错了');
      }, 1000);
  })
  const res = p.then((value) => {
      console.log(value);
      // 返回 Promise 对象
      return new Promise((resolve, reject) => {
          resolve('（1）成功了！！！');
          // reject('（1）出错了！！！')
      })
  }, (reason) => {
      console.warn(reason);
      return new Promise((resolve, reject) => {
          // resolve('（2）成功了！！！');
          reject('（2）出错了！！！')
      })
  })
  // 打印 then 方法的返回值
  console.log(res);Copy to clipboardErrorCopied
  ```

  打印结果：

  ![es6-5](https://cdn.jsdelivr.net/gh/Hacker-C/Picture-Bed@main/docs/es6-5.6z1msm3xzpc0.png)

- 如果回调函数中返回的结果是throw语句抛出异常，则then方法返回的对象状态为rejected，值为throw抛出的字面量或者Error对象。

  ```js
  const p = new Promise((resolve, reject) => {
      setTimeout(() => {
          resolve('用户数据');
      }, 1000);
  });
  const res = p.then((value) => {
      console.log(value);
      return new Promise((resolve, reject) => {
          throw new Error('错误了！！');
      })
  });
  // 打印结果
  console.log(res);Copy to clipboardErrorCopied
  ```

  打印结果如下：

   ![es6-6](https://cdn.jsdelivr.net/gh/Hacker-C/Picture-Bed@main/docs/es6-6.2c3aibc6l05c.png)

### 13.4 链式调用

`Promise.prototype.then` 方法返回的结果还是 `Promise` 对象，这意味着我们可以继续在该结果上使用 `then` 方法，也就是链式调用。

```js
const p = new Promise(resolve=>{}, reject=>{});
p.then(value=>{}, reason=>{})
.then(value=>{}, reason=>{})
.then(value=>{}, reason=>{})
...Copy to clipboardErrorCopied
```

### 13.5 链式调用练习-多个文件读取

```js
const fs = require('fs');

let p = new Promise((resolve, reject) => {
    fs.readFile('./resources/users.md', (err, data) => {
        // 传给下一轮文件读取操作
        resolve(data);
    })
});

p.then(value => {
    return new Promise((resolve, reject) => {
        // value 为第一次读取的文件数据，data 为第二次（当前）读取的数据
        fs.readFile('./resources/orders.md', (err, data) => {
            // 将上轮读取结果和本轮合并传到下一轮轮读取操作
            resolve([value, data]);
        });
    });
}).then(value => {
    return new Promise((resolve, reject) => {
        fs.readFile('./resources/goods.md', (err, data) => {
            // value 为上一轮传递过来的文件数据数组
            value.push(data);
            // 传给下一轮操作
            resolve(value);
        });
    });
}).then(value => {
    // 合并数组元素，输出
    console.log(value.join('\n'));
});Copy to clipboardErrorCopied
```

### 13.6 Promise.prototype.catch

`catch()` 方法返回一个 `Promise`，并且处理promise对象状态为拒绝的情况。它的行为与调用 `Promise.prototype.then(undefined, onRejected)` 相似，就是不给then方法指定第一个回调。

即：obj.catch(onRejected);等同于：obj.then(undefined, onRejected);

语法：

```js
p.catch(onRejected);

p.catch(function(reason) {
   // 拒绝
});
```

举例：

```js
var p1 = new Promise(function (resolve, reject) {
    resolve('Success');
});

p1.then(function (value) {
    console.log(value); // "Success!"
    throw 'oh, no!';
}).catch(function (e) {
    console.log(e); // "oh, no!"
}).then(function () {
    console.log('有 catch 捕获异常，所以这句输出');
}, function () {
    console.log('没有 catch 捕获异常，这句将不会输出');
});
```

输出结果：

```
Success
oh, no!
有 catch 捕获异常，所以这句输出
```

> 以上只是 Promise 的入门，更多还要进一步深入学习。

## 14. Set

### 14.1 Set 的定义和使用

ES6 提供了新的数据结构 `Set`（集合）。它类似于数组（类数组对象），但 **成员的值都是唯一的**，集合实现了 `iterator` 接口，所以可以使用『扩展运算符』和『`for...of`』进行遍历。typeof检查类型为object

定义一个 Set 集合：

```js
let st1 = new Set();
let st2 = new Set([可迭代对象]);
```

集合（这里假设有一个集合 `st`）的属性和方法：

- `st.size`：返回集合个数
- `st.add(item)`：往集合中添加一个新元素 `item`，返回当前集合
- `st.delete(item)`：删除集合中的元素，返回 `boolean` 值
- `st.has(item)`：检测集合中是否含某个元素，返回 `boolean` 值
- `st.clear()`：清空集合
- 集合转为数组：`[...st]`
- 合并两个集合：`[...st1, ...st2]`
- 无法通过下标获取元素

### 14.2 集合案例

案例1： 数组去重

```js
let arr1 = [1, 2, 2, 3, 3, 3, 4, 1, 2];
let res1 = [...new Set(arr1)];
console.log(res1); // [ 1, 2, 3, 4 ]
```

案例2：数组求交集

```js
let arr2_1 = [1, 2, 2, 3, 4, 5];
let arr2_2 = [3, 6, 6, 7, 1, 4];
let res2 = [...new Set(arr2_1)].filter(v => new Set(arr2_2).has(v))
console.log(res2); // [ 1, 3, 4 ]
```

案例3：数组求并集

```js
let arr3_1 = [1, 2, 2, 3, 4, 5];
let arr3_2 = [3, 6, 6, 7, 1, 4];
let res3 = [...new Set([...arr3_1, ...arr3_2])];
console.log(res3); // [ 1, 2, 3, 4, 5, 6, 7 ]
```

案例4：数组求差集

```js
let arr4_1 = [1, 2, 2, 3, 4, 5];
let arr4_2 = [3, 6, 6, 7, 1, 4];
let res4 = [...new Set(arr4_1)].filter(v => !(new Set(arr4_2).has(v)))
console.log(res4); // [ 2, 5 ]
```

## 15. Map

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合。但是 “键” 的范围不限于字符串，各种类型的值（包括对象）都可以当作键。Map 也实现了 iterator 接口，所以可以使用『扩展运算符』和『for...of』进行遍历。

**注意**：使用for of进行遍历得到是一个数组，第一个元素是键名，第二个是键值

定义一个 Map：

```js
let mp1 = new Map();
mp1.set('aaa', 111);
mp1.set('bbb', 222);
mp1.set('ccc', 333);

console.log(mp2.get('bbb')); // 222
```

Map 的属性和方法：（`k` 为键，`v`为值）

- `size`：返回 Map 的元素（键值对）个数
- `set(k, v)`：增加一个键值对，返回当前 Map
- `get(k)`：返回键值对的键值
- `has()`：检测 Map 中是否包含某个元素
- `clear()`：清空集合，返回 `undefined`
- 无法通过下标获取元素

## 16. class 类

这部分在 JS 高级也涉及，故可以前往 [JS 高阶 class](https://docs.mphy.top/#/JS-Advance/ch01) 学习。，那部分的笔记更加详细，有原理。所以这一节后面部分只给出例子。

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过class 关键字，可以定义类。基本上，ES6 的 `class` 可以看作只是一个语法糖，它的绝大部分功能，ES5 都可以做到，新的 `class` 写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。

### 16.1 声明类

```
 class Shouji{
            //构造方法 名字不能修改
            //没有构造方法也是合法的
            constructor(brand, price){
                this.brand = brand;
                this.price = price;
            }

            //方法必须使用该语法, 不能使用 ES5 的对象属性的完整形式
            call(){
                console.log("我可以打电话!!");
            }
        }
```

### 16.2 静态成员

在类中，用static关键字定义的属性是属于类的，不属于类的实例对象。简而言之，类的实例化对象不会有该属性

```

      class Phone {
        //静态属性
        static name = "手机";
        static change() {
          console.log("我可以改变世界");
        }
      }

      let nokia = new Phone();
      console.log(nokia.name); //undefined
      console.log(Phone.name); //手机
```

### 16.3 es5构造函数继承

```
   //手机
      function Phone(brand, price) {
        this.brand = brand;
        this.price = price;
      }

      Phone.prototype.call = function () {
        console.log("我可以打电话");
      };

      //智能手机
      function SmartPhone(brand, price, color, size) {
        //节省代码量
        Phone.call(this, brand, price);
        this.color = color;
        this.size = size;
      }

      //设置子级构造函数的原型，目的是可以使用父级的属性、方法
      SmartPhone.prototype = new Phone();
      SmartPhone.prototype.constructor = SmartPhone;

      //声明子类的方法
      SmartPhone.prototype.photo = function () {
        console.log("我可以拍照");
      };

      SmartPhone.prototype.playGame = function () {
        console.log("我可以玩游戏");
      };

      const chuizi = new SmartPhone("锤子", 2499, "黑色", "5.5inch");

      console.log(chuizi); //可以调用父级的call方法
```

### 16.4 extends 类继承和方法的重写

ES6 中直接使用 `extends` 语法糖（更简洁高级的实现方式）来实现继承，同时可以重写父类的方法，直接在子类中重新写一次要重写的方法即可覆盖父类方法。

```js
 class Phone {
        //构造方法
        constructor(brand, price) {
          this.brand = brand;
          this.price = price;
        }
        //父类的成员属性
        call() {
          console.log("我可以打电话!!");
        }
      }

      class SmartPhone extends Phone {
        //构造方法
        constructor(brand, price, color, size) {
          //子类调用父类的构造方法初始化
          super(brand, price); // Phone.call(this, brand, price)
          this.color = color;
          this.size = size;
        }

        photo() {
          console.log("拍照");
        }

        playGame() {
          console.log("玩游戏");
        }
        //方法重写
        //子类中不可以调用父类的同名方法
        call() {
          console.log("我可以进行视频通话");
        }
      }

      const xiaomi = new SmartPhone("小米", 799, "黑色", "4.7inch");
      // console.log(xiaomi);
      //继承的父类的方法，它在对象的隐式原型对象的原型对象上
      xiaomi.call();
      //这些方法都在对象的隐式原型上
      xiaomi.photo();
      xiaomi.playGame();
```

### 16.5 getter 和 setter

实际上，`getter` 和 `setter` 是 ES5（ES2009）提出的特性，这里不做详细说明，只是配合 `class` 使用举个例子。
当属性拥有 `get`/`set` 特性时，属性就是访问器属性。代表着在访问属性或者写入属性值时，对返回值做**附加的操作**。而这个操作就是 `getter`/`setter` 函数。

使用场景： `getter` 是一种语法，这种 `get` 将对象属性绑定到 **查询该属性时将被调用的函数**。适用于需要动态计算的成员属性值的获取。`setter` 则是在修改某一属性时所给出的相关提示。

```js
  //对对象的属性进行方法的绑定
      // get 和 set
      class Phone {
        //应用场景：需要动态计算的成员属性值
        get price() {
          console.log("价格属性被读取了");
          //返回值就是属性值
          return "iloveyou";
        }
        //应用场景：设置属性值时，先做判断在赋值
        set price(newVal) {
          console.log("价格属性被修改了");
        }
      }

      //实例化对象
      let s = new Phone();
      //读取属性调用price属性的方法，执行函数体，同时将返回值作为属性值
      // console.log(s.price);//价格属性被读取了   输出属性值
      s.price = "free";//价格属性被修改了
```

## 17. 数值扩展

1. `Number.EPSILON` 是 JavaScript 表示的最小精度，一般用来处理浮点数运算。例如可以用于两个浮点数的比较。

   ```js
   //当两个数值差小于Number.EPSILON ，我们认为它是相等的
   let equal = (x, y) => Math.abs(x - y) < Number.EPSILON;
   console.log(0.1 + 0.2 === 0.3); // false
   console.log(equal(0.1 + 0.2, 0.3)); // true
   ```

2. 二进制和八进制：二进制以 `0b` 开头，八进制以 `0o` 开头。

   ```js
   let b = 0b1010;
   let o = 0o777;
   let d = 100;
   let x = 0xff;
   console.log(x);
   ```

3. `Number.isFinite` 检测一个数值是否为有限数。

   ```js
   console.log(Number.isFinite(100)); // false
   console.log(Number.isFinite(100 / 0)); // true
   console.log(Number.isFinite(Infinity)); // false
   ```

4. Number.parseInt 和 Number.parseFloat
   ES6 给 `Number` 添加了 `parseInt` 方法，`Number.parseInt` 完全等同于 `parseInt`。将字符串转为整数，或者进行进制转换。`Number.parseFloat` 则等同于 `parseFloat()`

   ```js
   Number.parseInt === parseInt; // true
   Number.parseFloat === parseFloat; // true
   ```

   ```js
   Number.parseInt(s, base);
   ```

   - `s`：待转换的字符串

   - base：进位制的基数

     ```js
     console.log(Number.parseInt('5211314love')); // 5211314
     console.log(Number.parseFloat('3.1415926神奇')); // 3.1415926
     ```

5. `Number.isInteger()` 判断一个数是否为整数。

   ```js
   console.log(Number.isInteger(5)); // true
   console.log(Number.isInteger(2.5)); // false
   ```

6. `Math.trunc()` 将数字的小数部分抹掉。

   ```js
   console.log(Math.trunc(3.5)); // 3
   ```

7. `Math.sign` 判断一个数到底为正数 负数 还是零

   ```
   console.log(Math.sign(100)); //1
         console.log(Math.sign(0)); //0
         console.log(Math.sign(-20000)); //-1
   ```

8. Number.isNaN 检测一个数值是否为 NaN

    **isNaN 和 Number.isNaN 函数的区别？**

   - 函数 isNaN 接收参数后，会尝试将这个参数转换为数值，任何不能被转换为数值的的值都会返回 true，因此非数字值传入也会返回 true ，会影响 NaN 的判断。
   - 函数 Number.isNaN 会首先判断传入参数是否为数字，如果是数字再继续判断是否为 NaN ，不会进行数据类型的转换，这种方法对于 NaN 的判断更为准确。

## 18. 对象方法扩展

ES6 新增了一些 `Object` 对象的方法。

- Object.is 比较两个值是否严格相等，与『===』行为 **基本一致**，但它判断NaN与自身是否相等，会返回true
- `Object.assign` 对象的合并，将源对象的所有可枚举属性，复制到目标对象
- `__proto__`、`setPrototypeOf`、`setPrototypeOf` 可以直接设置对象的原型

### 18.1 Object.is()

`Object.is` 比较两个值是否严格相等，与 `===` 行为 **基本一致**。返回一个 `Boolean` 类型。但它判断NaN与自身是否相等，会返回true

```js
Object.is(value1, value2);
```

`Object.is()` 方法判断两个值是否为同一个值。如果满足以下条件则两个值相等：

- 都是 `undefined`
- 都是 `null`
- 都是 `true` 或 `false`
- 都是相同长度的字符串且相同字符按相同顺序排列
- 都是相同对象（意味着每个对象有同一个引用）
- 都是数字且
  - 都是 `+0`
  - 都是 `-0`
  - 都是 `NaN`
  - 或都是非零而且非 `NaN` 且为同一个值

与 `==` 运算不同。 `==` 运算符在判断相等前对两边的变量（如果它们不是同一类型）进行强制转换 (这种行为的结果会将 `"" == false` 判断为 `true`)，而 `Object.is` 不会强制转换两边的值。

与 `===`算也不相同。 === 运算符 (也包括 `==` 运算符) 将数字 `-0` 和 `+0` 视为相等，而将 `Number.NaN` 与 `NaN` 视为不相等。

### 18.2 Object.assign

`Object.assign` 对象的合并，相当于浅拷贝。后面的对象会覆盖前一个对象相同的属性，不同的属性会合并

```js
const config1 = {
    host: 'localhost',
    port: 3306,
    name: 'root',
    pass: 'root',
    test: 'test'
};
const config2 = {
    host: 'http://atguigu.com',
    port: 33060,
    name: 'atguigu.com',
    pass: 'iloveyou',
    test2: 'test2'
}
console.log(Object.assign(config1, config2))
```

### 18.3 Object.setPrototypeOf 和 Object.getPrototypeof

`Object.setPrototypeOf` 用于设置对象的原型对象，`Object.getPrototypeof` 用于获取对象的原型对象，相当于 `__proto__`。

```js
const school = {
    name: '尚硅谷'
}
const cities = {
    xiaoqu: ['北京','上海','深圳']
}
Object.setPrototypeOf(school, cities);
console.log(Object.getPrototypeOf(school));
console.log(school);
```

## 19. ES6 模块化

模块化是指将一个大的程序文件，拆分成许多小的文件，然后将小文件组合起来。

### 19.1 模块化的好处

模块化的优势有以下几点：

- 防止命名冲突
- 代码复用
- 高维护性

### 19.2 模块化规范产品

ES6 之前的模块化规范有：

- CommonJS => NodeJS、Browserify
- AMD => requireJS
- CMD => seaJS

### 19.3 ES6 模块化语法

模块功能主要由两个命令构成：`export` 和 `import`。

- `export` 命令用于规定模块的对外接口
- `import` 命令用于输入其他模块提供的功能

#### 19.3.1 模块导出数据语法

1. 统一暴露

   通过【变量.暴露的属性名】获取暴露的数据

   ```
   let uname = 'Rick';
   let sayHello = function () {
       console.log('Hi, bro!');
   }
   // 合并导出
   export { uname, sayHello };
   ```

2. 分别暴露

   通过【变量.暴露的属性名】获取暴露的数据

   ```
   // 单个导出
   export let uname = 'Rick';
   export let sayHello = function () {
       console.log('Hi, bro!');
   }
   ```

3. 默认暴露

   暴露的数据是作为对象的default属性对象保存，通过【变量.default.暴露的属性名】获取暴露的数据

   ```js
   // 默认导出
   export default {
       uname: 'Rick',
       sayHello: function () {
           console.log('Hi, bro!');
       }
   }
   ```

#### 19.3.2 模块导入数据语法

需要用live server打开页面，否则会出现跨域问题

1. 通用导入方式

   需要【变量.暴露的属性名】获取暴露的数据。

   如果导入的是**默认暴露**的数据，需要【变量.default.暴露的属性名】获取暴露的数据

   ```js
   <!-- 以前的js文件导入方式 -->
       <script src=""></script>
       <!-- 模块导入文件的方式 -->
       <script type="module">
         // 引入文件
         // 导入的数据作为m1的属性
             //引入 m1.js 模块内容
           // import * as m1 from "./src/js/m1.js";
           // //引入 m2.js 模块内容
           // import * as m2 from "./src/js/m2.js";
           // //引入 m3.js 
           // import * as m3 from "./src/js/m3.js";
       </script>
   ```

2. 解构赋值导入

   可以直接使用暴露的数据，不需要【变量.暴露的属性名】获取暴露的数据，解构赋值的变量名需要和导入模块中暴露的变量名相同

   ```js
     //分别暴露和统一暴露的导入方式
         import { school, teach } from "./src/js/m1.js";
         //出现重名时需要用as取别名
         import { school as guigu, findJob } from "./src/js/m2.js";
         //针对默认暴露，必须对default取别名
         import { default as m3 } from "./src/js/m3.js";
   ```

3. 简便方式导入，针对默认暴露

   暴露的数据作为m3对象的属性保存

   ```js
   import m3 from "./src/js/m3.js";
   ```

#### 19.3.3 ES6 使用模块化方式导入

将文件导入都写进一个 app.js 文件中，然后在里面写入要导入的模块。app.js 中的内容如下：

```js
import * as m1 from './js/m1.js';
import * as m2 from './js/m2.js';
import * as m3 from './js/m3.js';

console.log(m1);
console.log(m2);
console.log(m3);
```

在 index.html 中引入 app.js 文件内容：

```html
<script src="./app.js" type="module"></script>
```

##### 19.3.3.3.1在实际开发中不选择这些方式：

1、存在兼容性问题，不是所有浏览器都像chrome支持所有的特性

2、es6模块开发不能对npm安装的模块做一个导入

解决方式：Babel

### 19.4 使用 babel 对模块化代码转换

是一个JavaScript编译器，将ES6 新特性语法转化为浏览器可以识别的es5语法

1. 安装工具

   ```
   //局部安装，在命令行使用Babel通过npx babel
   npm i babel-cli babel-preset-env browserify(webpack) -D
   //全局安装，命令行直接通过babel使用Babel
   npm i babel-cli babel-preset-env browserify(webpack) -D -g
   ```

   babel-cli：Babel命令行工具

   babel-preset-env：Babel环境预设包，能够将es6最新的特性转化为es5的语法

   browserify：打包工具

   -D：开发依赖，这个包只在开发环境用，发布生产时不会打包进去

2. 编译

   ```
   npx babel src/js -d dist/js --presets=babel-preset-env
   ```

   src/js：原文件目录

   dist/js：打包文件目录

3. 打包

   目的：浏览器不认识require等语法，通过打包将它们变成浏览器认识的语法

   dist/js/app.js:编译后的文件

   -o：输出

   dist/bundle.js：打包文件位置

   ```
   npx browserify dist/js/app.js -o dist/bundle.js
   ```

4. 导入模块

   ```
   <script src="dist/bundle.js"></script>
   ```

### 19.5 ES6 模块化引入 npm 安装的包

```
npm install jquery
```

再通过 `import` 导入即可。 

![image-20230317164456157](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20230317164456157.png)

# 第二章 es7新特性

## 1. Array.prototype.includes

`includes` 方法用来检测数组中是否包含某个元素，返回布尔类型值。

## 2. 指数运算符

在 ES7 中引入指数运算符 `**`，用来实现幂运算，功能与 `Math.pow(a, b)` 结果相同。

```js
2 ** 3 // 8
Math.pow(2, 3) // 8
```

# 第三章 es8新特性

## 1. async 和 await

`async` 和 `await` 两种语法结合可以让异步代码像同步代码一样。（即：看起来是同步的，实质上是异步的。）同样是异步编程的解决方案

先从字面意思理解，`async` 意为异步，可以用于声明一个函数前，该函数是异步的。`await` 意为等待，即等待一个异步方法完成。

### 1.1 async

`async` 声明（`function`）的函数成为 async 函数，语法：

```js
async function funcName() {
    //statements 
}
```

`async` 内部可以使用 `await`，也可以不使用。 `async` 函数的返回值是一个 `Promise` 对象，因此执行这个函数时，可以使用 `then` 和 `catch` 方法。 根据 **函数体内部** 的返回值， `async` 函数返回值具体情况如下：

- 函数体内不返回任何值，则async函数返回值为一个成功（fulfilled）的Promise对象，状态值为undefine。

  ```js
  let a = async function() {}
  let res = a()
  console.log(res)
  // Promise{<fullfilled>: undefined}
  ```

- 返回结果不是一个Promise类型对象，则async函数返回值为一个成功（fulfilled）的Promise对象，状态值为这个内部返回值。

  ```js
  let a = async function () {
    return 'hello'
  }
  let res = a()
  console.log(res) 
  // Promise{<fullfilled>: 'hello'}
  ```

- 内部抛出错误，则async函数返回值为一个失败（rejected）的Promise对象。

  ```js
  let a = async function foo() {
    throw new Error('出错了')
  }
  a().catch(reason => {
    console.log(reason)
  })
  ```

- 若函数内部返回值是一个Promise对象，则async函数返回值的状态取决于这个Promise对象。

  ```js
  let a = async function () {
    return new Promise((resolve, reject) => {
      resolve("成功")
    })
  }
  a().then(value => {
    console.log(value)
  })Copy to clipboardErrorCopied
  ```

### 1.2 await

综合 `async` 和 `await` 而言。`await` 必须结合 `async` 使用，而 `async` 则不一定需要 `await`。

`await` 相当于一个运算符，右边接一个值。一般为一个 `Promise` 对象，也可以是一个非 `Promise` 类型。当右接一个非 `Promise` 类型，`await` 表达式返回的值就是这个值；当右接一个 `Promise` 对象，则 `await` 表达式会阻塞后面的代码，等待当前 `Promise` 对象 的值。当这个Promise` 失败或者抛出异常时，需要时使用 `try-catch` 捕获处理。

在async函数中可以有多个await，如果其中某一个await接收的是rejected状态的promise对象，那么整个async函数都会中断执行（无法执行）。

`Promise` 使用链式调用解决了传统方式回调地狱的问题，而 `async-await` 又进一步优化了代码的可读性。

```js
const p = new Promise((resolve, reject)=>{
  resolve('成功')
})
async function main() {
  let res = await p
  console.log(res)
}
main()
// '成功'
const p = new Promise((resolve, reject)=>{
  reject('失败')
})
async function main() {
  try {
    let res = await p
    console.log(res)
  } catch(e) {
    console.log(e)
  }
}
main()
// '失败'
```

### 1.3 综合应用-读取文件

需求：先读取用户数据 user，然后读取订单数据 order，最后读取商品数据 goods。

对于这种异步操作很容易想到使用 `Promise`，代码如下：

```js
const fs = require('fs')

let p = new Promise((resolve, reject) => {
  fs.readFile('./files/user.md', (err, data) => {
    if (err) reject(err)
    resolve(data)
  })
})

p.then(value => {
  return new Promise((resolve, rejecet) => {
    fs.readFile('./files/order.md', (err, data) => {
      if (err) rejecet(err)
      resolve([value, data])
    })
  })
}, reason => {
  console.log(reason)
}).then(value => {
  return new Promise((resolve, reject) => {
    fs.readFile('./files/goods.md', (err, data) => {
      if (err) reject(err)
      value.push(data)
      resolve(value)
    })
  })
}, reason => {
  console.log(reason)
}).then(value => {
  console.log(value.join('\n'))
}, reason => {
  console.log(reason)
})
```

但是，使用 `Promise` 链式调用虽然避免了回调地狱，但这种链式调用过多难免引起代码复杂，看起来不直观。可以使用 `async` 和 `await` 方法优化，代码如下：

```js
const fs = require('fs')

function readUser() {
  return new Promise((resolve, reject) => {
    fs.readFile('./files/user.md', (err, data) => {
      if (err) reject(err)
      resolve(data)
    })
  })
}

function readOrder() {
  return new Promise((resolve, reject) => {
    fs.readFile('./files/order.md', (err, data) => {
      if (err) reject(err)
      resolve(data)
    })
  })
}

function readGoods() {
  return new Promise((resolve, reject) => {
    fs.readFile('./files/goods.md', (err, data) => {
      if (err) reject(err)
      resolve(data)
    })
  })
}

async function read() {
  let user = await readUser()
  let order = await readOrder()
  let goods = await readGoods()
  console.log([user, order, goods].join('\n'))
}

read()
```

这样，代码看起来很直观，就好像是同步代码一样，实际上是异步操作。

在async函数中可以有多个await，如果其中某一个await接收的是rejected状态的promise对象，那么整个async函数都会中断执行（无法执行）。用try-catch解决

### 1.4 综合应用-封装ajax

```js
function sendAjax(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.open('get', url)
    xhr.send()
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve(JSON.parse(xhr.response))
        }
        reject(xhr.status)
      }
    }
  })
}

async function main() {
  let res = await sendAjax('http://poetry.apiopen.top/sentences')
  let poem = res.result.name + '——' + res.result.from
  document.body.innerText = poem
}

main()
```

这里封装的ajax还不能体现 `async-await` 的作用所在，因为没有出现多个 ajax 请求。在又多个 ajax 请求并且后续的请求依赖于前一个请求的结果的时候，`async-await` 的优点就体现出来了。

## 2. 对象方法的扩展

- Object.values()方法返回对象的所有键值，并且封装为数组

- Object.keys()返回对象的所有键名，并且封装为数组

  ```
  let obj = {
    a: 1,
    b: {1:2},
    c: [1,2,3]
  }
  console.log(Object.values(obj))
  // [1, {1: 2}, [1,2,3]]
  console.log(Object.keys(obj))
  // ['a', 'b', 'c']
  ```

- Object.getOwnPropertyDescriptors（）返回对象属性的描述对象

  ```
  const school = {
          name: "尚硅谷",
          cities: ["北京", "上海", "深圳"],
          xueke: ["前端", "Java", "大数据", "运维"],
        };
  console.log(Object.getOwnPropertyDescriptors(school));
  ```

  创建具有描述对象属性的对象

  ```
   const obj = Object.create(null, {
            name: {
                //设置值
                value: '尚硅谷',
                //属性特性
                //可写的
                writable: true,
                //可删除
                configurable: true,
                //可枚举
                enumerable: true
            }
        });
  ```

- ，Object.entries()方法返回一个对象自身可遍历属性[key,value]的数组（数组元素也是一个个的数组的数组），将对象转化为**二维数组**，对象有几个属性，就有几个数组元素，与object.fromEntries()互为逆运算

  ```js
  const obj = {a: 1, b: 2, c: 3};
  console.log(Object.entries(obj))
  // [ [ 'a', 1 ], [ 'b', 2 ], [ 'c', 3 ] ]
  ```

  返回的是一个数组，这样就可以使用for...of遍历了。

  ```js
  const obj = { a: 1, b: 2, c: 3 };
  for (let [k, v] of Object.entries(obj)) {
    console.log(k, v)
  }
  ```

# 第四章 es9新特性

## 1、扩展运算符和rest参数

Rest 参数与 spread 扩展运算符在 ES6 中已经引入，不过 ES6 中只针对于数组，在 ES9 中为对象提供了像数组一样的 rest 参数和扩展运算符

扩展运算符

```
 //对象合并
 const mangseng = {...skillOne, ...skillTwo, ...skillThree, ...skillFour};
```

rest参数

```
function connect({host, port, ...user}){
            console.log(host);
            console.log(port);
            console.log(user);//{username: 'root',password: 'root',type: 'master'}
        }

        connect({
            host: '127.0.0.1',
            port: 3306,
            username: 'root',
            password: 'root',
            type: 'master'
        });

```

## 2、正则扩展

### 2.1 命名捕获分组

以前提取内容的方式：

```
//声明一个字符串
      let str = '<a href="http://www.atguigu.com">尚硅谷</a>';
      //.*表示匹配除了换行符之外的任何单个或多个字符
      //提取 url 与 『标签文本』
      const reg = /<a href="(.*)">(.*)<\/a>/;

      //执行
      const result = reg.exec(str);
      // result是数组，第一个元素是整个正则匹配的结果，第二个元素是第一个(.*)匹配的结果，第二个元素是第二个(.*)匹配的结果
      console.log(result);
      // console.log(result[1]);
      // console.log(result[2]);
```

![image-20230318102251653](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20230318102251653.png)

命名捕获分组：

```
let str = '<a href="http://www.atguigu.com">尚硅谷</a>';
      //分组命名
      const reg = /<a href="(?<url>.*)">(?<text>.*)<\/a>/;
      //比上一种方式多了groups属性，可以通过它获取内容
      const result = reg.exec(str);

      console.log(result.groups.url);

      console.log(result.groups.text);
```

![image-20230318102743682](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20230318102743682.png)

### 2.2 断言

正向断言：

```
   //声明字符串
        let str = 'JS5211314你知道么555啦啦啦';
        //正向断言，提取555
        const reg = /\d+(?=啦)/;
        const result = reg.exec(str);
```

反向断言：

```
const reg = /(?<=么)\d+/;
        const result = reg.exec(str);
```

### 2.3 dotAll模式

# 第五章 es10新特性

## 1. Object.fromEntries

Object.fromEntries() 方法把可迭代对象的键值对列表转换为一个对象。传入二维数组或map对象作为参数，把二维数组或map对象转化为**对象**，与object.entries()互为逆运算
语法：

```js
Object.fromEntries(iterable)
```

- `iterable`：类似 Array 、 Map 或者其它实现了可迭代协议的可迭代对象。
- 返回值：一个由该迭代对象条目提供对应属性的新对象。
- 相当于 `Object.entries` （ES8）的逆运算。

```js
const mp = new Map([
  [1, 2],
  [3, 4]
])
const obj = Object.fromEntries(mp)
console.log(obj)
// { '1': 2, '3': 4 }
const arr = [[1, 2]]
console.log(Object.fromEntries(arr))
// {'1': 2}
```

## 2. trimStart() 和 trimEnd()

- `trimStart()` 去除字符串开头连续的空格（`trimLeft` 是此方法的别名）
- `trimEnd()` 去除字符串末尾连续的空格（`trimRight` 是此方法的别名）
- 会影响原字符串

## 3. Array.prototype.flat 和 Array.prototype.flatMap

- Array.prototype.flat(i)：将多维数组转化为低位数组，i为要展开的层数，默认为1，即展开一层。旧维度-i=新维度，新维度值为0或1，都是在一维数组

  ```js
  let arr1 = [1, [2, 3], [4, 5]]
  console.log(arr1.flat(1)) 
  // [1,2,3,4,5]
  let arr2 = [1, [2, 3, [4, 5]]]
  console.log(arr2.flat(2))
  // [1,2,3,4,5]
  ```

  使用Infinity作为深度，展开任意深度的嵌套数组

  ```js
  [1, [2, 3, [4, 5]]].flat(Infinity)
  // [1, 2, 3, 4, 5, 6]
  ```

  也可以使用flat来去除数组空项

  ```js
  let arr = [1,2,3,,4]
  arr.flat() // [1,2,3,4]
  ```

- Array.prototype.flatMap：相当于map和flat的结合，如果map方法返回的是一个多维数组，但我们想得到一个一维数组，就可以使用flatMap方法。

  ```js
  let arr = [1,2,3,4]
  let res1 = arr.map(x => [x ** 2])//二维数组
  console.log(res1)
  // [[1],[4],[9],[16]]
  let res2 = arr.flatMap(x => [x ** 2])//一维数组
  console.log(res2)
  // [1,4,9,16]
  ```

## 4. Symbol.prototype.description

使用 `Symbol()` 创建的 `Symbol` 字面量，可以直接使用 `description` 获取该字面量的描述。

```js
let sym = Symbol('hello')
console.log(sym.description)
// hello
```

# 第六章 es11新特性

## 1. 类的私有属性

ES11 提供了类的私有属性，在类的外部无法访问该属性。只有再类的内部才能访问和操作。

```js
class Person{
  //公有属性，可以省略
  name;
  //私有属性，必须在构造方法前声明
  #age;
  #weight;
  //构造方法
  constructor(name, age, weight){
    this.name = name;
    this.#age = age;
    this.#weight = weight;
  }

  intro(){
    console.log(this.name);
    console.log(this.#age);
    console.log(this.#weight);
  }
}

//实例化
const girl = new Person('晓红', 18, '45kg');

// 外部无法直接访问
// console.log(girl.name);
// console.log(girl.#age);
// console.log(girl.#weight);

girl.intro();
```

## 2. allSettled

 `Promise.allSettled()`，将promise对象封装为数组作为参数传入 。`allSettled` 方法**返回的 `Promise` 对象的状态（status）始终是成功（`fulfilled`）的**，promise对象的值（value）是一个数组，数组元素**是包含了status和value属性的对象，属性值是对应promise对象**
使用场景：

- 有多个彼此不依赖的异步任务成功完成时使用。
- 想得到每个 `promise` 的结果时使用。

对比于 `Promise.all()`，`all()` 也接受一个 `Promise` 对象封装的数组作为参数，只要**数组中的promise对象有一个失败（`rejected`），那么返回的 `Promise` 对象就是失败（`rejected`）的，值就是失败的promise对象的值**，如果数组中的promise对象都是成功的，那么返回的promise对象的状态就是成功的，它的值是一个数组，数组中的元素是**参数数组中对应的promise对象的值**
使用场景：

- 传进去的 `Promise` 对象彼此依赖，且需要在其中任何一个失败的时候停止。

两个 `Promise` 都是成功的情况：

```js
let p1 = new Promise((resolve, reject) => {
  resolve('用户数据-1')
})

let p2 = new Promise((resolve, reject) => {
  resolve('订单数据-2')
})

let res1 = Promise.allSettled([p1, p2])
let res2 = Promise.all([p1, p2])
console.log(res1)
console.log(res2)
```

输出结果： ![image-20230318151416164](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20230318151416164.png)

一个成功，一个失败：

```js
let p1 = new Promise((resolve, reject) => {
  resolve('用户数据-1')
})

let p2 = new Promise((resolve, reject) => {
  reject('失败了')
})

let res1 = Promise.allSettled([p1, p2])
let res2 = Promise.all([p1, p2])
console.log(res1)
console.log(res2)
```

打印结果： ![image-20230318151849386](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20230318151849386.png)

## 3. matchAll

`matchAll()` 方法返回一个包含所有匹配正则表达式的结果及分组捕获组的迭代器。

```js
const regexp = /t(e)(st(\d?))/g;
const str = 'test1test2';

const array = [...str.matchAll(regexp)];

console.log(array[0]);
// expected output: Array ["test1", "e", "st1", "1"]

console.log(array[1]);
// expected output: Array ["test2", "e", "st2", "2"]
```

## 4. 可选链操作符

### 4.1 定义

可选链 `?.` 是一种安全访问嵌套对象属性的方式。即使中间的属性不存在，也不会出现错误。
原则：如果可选链 `?.` 前面的部分是 `undefined` 或者 `null`(属性不存在)，它会停止运算并返回 undefined。

```js
let user = {
  address: {
  }
}
console.log( user?.address?.street ); // undefined（不报错）
//是user&&user.address&&user.address.street 形式的优化，如果中间断了，会返回undefined
```

### 4.2 短路效应

短路效应：正如前面所说的，如果 `?.` 左边部分不存在，就会立即停止运算（“短路效应”）。所以，如果后面有任何函数调用或者副作用，它们均不会执行。
这有和 `&&` 的作用类似，但上述改用 `&&` 会显得代码冗余度高：

```js
console.log(user && user.address && user.address.street)
```

### [4.3 其它变体：?.()，?.[\]]

可选链 `?.` 不是一个运算符，而是一个特殊的语法结构。它还 **可以与函数和方括号一起使用**。
例如，将 `?.()` 用于调用一个可能不存在的函数（即使不存在也不报错）。

```js
function foo() {
  console.log('hello')
}
foo?.()
```

`?.[]` 允许从一个可能不存在的对象上安全地读取属性。（即使不存在也不报错）。

```js
let obj = {
  key: 123
}
console.log(obj?.['key'])
// 123
```

## 5. 动态 import 导入

解决通用、文件导入模块时，不能懒加载（按需引用）的问题

它会返回一个promise对象，调用then方法，传入的值（promise对象的值）是一个包含暴露属性的对象

```js
const btn = document.getElementById('btn');

btn.onclick = function(){
  import('./hello.js').then(module => {
    module.hello();
}
```

## 6. BigInt

`BigInt` 是一种特殊的数字类型，它提供了对任意长度整数的支持。

创建 `bigint` 的方式有两种：(1)在一个整数字面量后面加 `n` (2)调用 `BigInt` 函数，该函数从字符串、数字等中生成 `bigint`，参数必须是整数，不能是浮点数。

typeof检查类型返回‘bigint’

bigInt的数据不能和number数据进行运算，必须对number数据用`BigInt` 函数处理

```js
let n1 = 123n
let n2 = 456n
let n3 = BigInt(789)
console.log(typeof n1) // bigint
console.log(n1+n2) // 579n
console.log(n2+n3) // 1245n
```

比较运算符：

- 例如<和>，使用它们来对bigint和 number 类型的数字进行比较没有问题：

  ```js
  alert( 2n > 1n ); // true
  alert( 2n > 1 ); // true
  ```

- 但是请注意，由于number和bigint 属于不同类型，它们可能在进行==比较时相等，但在进行===（严格相等）比较时不相等：

  ```js
  alert( 1 == 1n ); // true
  alert( 1 === 1n ); // false
  ```

## 7. globalThis

在浏览器中（HTML文件）,`globalThis` 就是指向window全局对象

在node.js中，`globalThis` 就是global全局对象

全局对象提供可在任何地方使用的变量和函数。默认情况下，这些全局变量内置于语言或环境中。
在浏览器中，它的名字是 `window`，对 Node.js 而言，它的名字是 `global`，其它环境可能用的是别的名字。
ES11中 `globalThis` 被作为全局对象的标准名称加入到了 JavaScript 中，所有环境都应该支持该名称。所有主流浏览器都支持它。
使用场景： 假设我们的环境是浏览器，我们将使用 `window`。如果你的脚本可能会用来在其他环境中运行，则最好使用 `globalThis`。

