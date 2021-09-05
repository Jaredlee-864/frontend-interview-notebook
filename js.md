## 1. 原型/构造函数/实例
  - 原型（prototype）：一个简单的对象，**用于实现对象的属性继承**。简单来说，可以理解为对象的爹。在 Firefox 和 Chrome 中，每个`Javascript` 对象中都包含一个 `__proto__` 属性指向它爹（该对象的原型），可通过 `obj.__proto__` 来访问
  - 构造函数(constructor): 可以通过 `new` 关键字来**新建一个对象**的函数
  - 实例：通过构造函数和 `new` 关键字创建出来的对象，叫实例。**实例通过 `__proto__` 指向原型，通过 `constructor` 指向构造函数**
  - 总结为一句话就是 `obj.__proto__ = obj.contructor.prototype`。即实例的 `__proto__` 属性，指向其构造函数的 `prototype` 属性，两者是相等的，都为原型

## 2. 原型链
> 原型链是由原型对象构成的。每个实例对象都有 **__proto__** 属性，指向了创建该对象的构造函数的原型，**__proto__** 将对象连接起来组成了原型链。是一个用来 **实现继承和共享属性** 的有限的对象链。

- **属性查找机制**：当查找对象的属性时，如果实例对象的自身不存在该属性，就会沿着原型链往上一级查找，找到时输出，找不到则继续沿着原型链向上查找，直至最顶层的原型对象 `Object.prototype`, 如果还是没找到，则输出 `undifined`

- **属性修改机制**：只会修改实例对象本身的属性，如果不存在，则创建新的属性。如果需要修改原型上的属性，则可以用 `obj.prototype.x = 2`; 不过这样会造成所有继承于该对象的实例的属性发生改变

## 3. 执行上下文（EC：Execute Context）
> 执行上下文可以简单的理解为一个对象：

- 它包括三个部分
  1. 变量对象（VO: Virable Object）
  2. 作用域链（词法作用域）
  3. `this` 指向

- 它的类型有：
  1. 全局执行上下文（通常简称为 ECG）
  2. 函数执行上下文（通常简称为 ECF）
  3. `eval` 执行上下文

- 代码执行过程
  - 创建全局执行上下文(Global EC)
  - 全局执行上下文(caller) **自上而下** 执行。遇到函数时，函数执行上下文（callee）被`push`到执行栈的顶层
  - 函数执行上下文被激活（即函数调用时），成为 active EC，开始执行函数中的代码，caller 被挂起
  - 函数执行完成后，callee 被 `pop` 移出执行栈，控制权交还全局执行上下文（caller），继续执行
### 变量对象（VO）

> 变量对象是执行上下文中的一部分，可以抽象成一种 **数据作用域**。简单的理解，VO就是一个对象，该对象存储着执行上下文中的 **变量和函数声明（不包含函数表达式）**

> 活动对象（AO：Active Object）: 当变量对象所处的上下文为 Active EC时，成为活动对象

### 作用域

> 执行上下文中还包含了作用域链。作用域，可以理解为执行上下文当中的 **变量和声明的作用范围**。可分为 **块级作用域** 和 **函数作用域**

> - 特性：

> - 声明提前（作用域提升）：一个声明在函数体内都是可见的，函数优先于变量

> - 非匿名自执行函数，函数变量为 **只读** 状态，无法修改 // TODO:

> ```js
> let foo = function() { console.log(1) };
> (function foo() {
>   foo = 10  // 由于foo在函数中只为可读，因此赋值无效
>   console.log(foo)}
> ()) 
> // 结果打印：  ƒ foo() { foo = 10 ; console.log(foo) }
> ```

### 作用域链

我们可以在执行上下文中访问到自身父级甚至全局的变量，这便是作用域的功劳。作用域链可以理解为一组对象列表，包含 **父级和自身的变量对象**，因此我们便能通过作用域链访问到父级里声明的变量和函数

- 由两部分组成
  - `[[scope]]`属性：指向父级变量对象和作用域链，也就是包含了父级的`[[scope]]` 和 `AO`
  - AO: 自身的活动对象

如此，`[[scope]]`包含`[[scope]]`，便自上而下形成了一条作用域链。

## 4. 闭包(Closure)

函数和其周围的状态（词法环境）的引用捆绑在一起形成闭包。通俗点说就是，可以在另外一个作用域中调用一个函数的内部函数并访问到该函数的作用域中的成员。

闭包的本质：函数在执行的时候会被push到一个执行栈上，当函数执行完毕时，会从栈上移除。**但是堆上的作用域成员因为被外部引用而不能释放**，因此内部函数依然可以访问外部函数的成员

## 5. script 引入方式

- html 静态`<script>`标签引入
- js 动态插入`<script>`
- `<script defer>`: 延迟加载，元素解析完成后执行
- `<script async>`: 异步加载，但执行时会阻塞元素渲染

## 6. Set/weakSet/Map/weakMap
- Set
  - 是一种叫做集合的数据结构
  - 成员唯一、无序且不重复
  - 允许存储任意类型的唯一值，无论是原始类型还是引用引用类型
  - 可以遍历，方法有 `add`, `has`, `delete`, `clear`

- WeakSet
  - 拥有 Set 的特性，但是成员都是对象
  - 而且对象都是弱引用，可以被垃圾回收机制回收，可以用来保存 `DOM` 节点，不容易造成内存泄漏
  - 不能遍历，方法有`add`, `delete`, `has`

- Map
  - 是一种类似于数据字典的数据结构，本质上是键值对的集合。相对于对象 Object 的键名只能是 `String` 或者 `Symbol` 来说，Map 的键值可以是任何值，不会被转换
  - 可以遍历，可以跟各种数据格式转换（转数组，转对象）
  - 操作方法有：`set`, `get`, `has`, `delete`, `clear`

- WeakMap
  - 只接受对象作为键名（`null` 除外），不接受其他类型的值作为键名
  - 键名是弱引用，键名所指向的对象可以被垃圾回收机制回收，回收过后键名是无效的
  - 不能遍历，方法有 `set`, `get`, `has`, `delete`

- Set 和 Map 的遍历方法有 `keys`, `values`, `entries`, `forEach`

## 7. 深拷贝与浅拷贝

- 浅拷贝：以赋值的形式拷贝对象的引用，扔指向同一个地址，修改时原对象也会受到影响
  - Object.assign()
  - 扩展运算符（...）

- 深拷贝：完全拷贝一个新对象，修改时原对象不再受到任何影响
  - JSON.parse(JSON.stringify(obj)): 性能最快
    - 具有循环引用的对象是，报错
    - 当值为函数、undefined、symbol时，无法拷贝
  - 递归进行逐一赋值

## 8. new 运算符的执行过程
- 生成一个新对象
- 链接到原型：obj.__proto__ = constructor.prototype
- 绑定this
- 返回新对象（如果构造函数有 return 时，返回 return 的值）

## 9. instanceof 的原理

能在实例的 **原型链** 中找到该构造函数的 `prototype` 所指向的原型对象，就返回 `true`

## 10. 代码的复用

当发现任何代码需要开始写第二遍的时候，就要开始考虑如何复用了。有以下几种方式

- 函数封装
- 继承
- 复制 `extend`
- 混入 `mixin`
- 借用 `apply / call`

## 11. 继承

在js中，继承通常指的是 **原型链继承**，也就是通过指定原型，可以继承原型链上的属性和方法。

- 最优化：**圣杯模式**
```js

```

- 使用 ES6 的语法糖 `class / extends`

## 12. js 性能优化

- 对于不使用的对象（函数，变量等），手动置为 null，触发垃圾回收机制
- 减少闭包使用
- 使用事件委托
- 缓存数据：对于需要多次使用的数据进行提前保存，后续进行使用（函数里面声明变量对多次使用的需要访问的数据进行缓存）
- 变量局部化
- 减少访问的层级
- 防抖与节流
- 减少判断层级
  - 如果有多层 if 判断嵌套，考虑是否可以用 return 提前结束判断，减少判断的层级
  - 如果有多个 if else 判断，考虑使用 Switch case
- 减少循环体活动
- 字面量和构造式
  - 字面量更快（对于基本类型来说，字面量快很多，引用类型差别不大）

## 13. 类型转换

JS在使用运算符号或者对比符是，会发生隐式类型转换，规则如下：

- -、*、/、%：一律转换成数值后计算
- +：
  - 数字 + 字符串 = 字符串，运算顺序是从左到右
  - 数字 + 对象， 有限调用对象的 `valueOf` -> `toString`
  - 数字 + `boolean/null` -> 数字
  - 数字 + undefined -> NaN
- `[1].toString() === '1'`
- `{}.toString() === [object object]`
- `NaN !== NaN` `+undefined 为 NaN`

## 14. 类型判断

单单使用 typeof 并无法完全满足，这其实不是bug，本质原因是 JS 的万物皆对象的理论。因此要真正完美判断是，我们需要区分对待：

- 基本类型(`Null`): 使用 `String(null) === 'null'`
- 基本类型（`string / number / boolean / undefined`） + `function`: 直接使用`typeof`即可
- 其余引用类型（`Array / Date / RegExp Error`）: 调用`toString` 后根据`[object xxx]` 进行判断

## 15. 模块化

模块化开发在现代开发中已经是必不可少的一部分。它大大提高了项目的可维护、可扩展、和可协作性。通常，我们 **在浏览器中使用 ES6 的模块化支持，在 Node 中使用 commonJS 的模块化支持**。

- 分类
  - es6: `import/export`
  - commonjs: `require / module.exports / exports`
  - amd: `require / defined`

- `require / import` 的区别
  - `require` 是 **同步** 导入，`import` 属于 **异步** 导入
  - `require` 是 **值拷贝**，导出值变化不会影响导入值；`import`指向内存地址，导入值会随导出值而变化

## 16. 防抖与节流

防抖和节流函数是一种常见的 **高频触发优化方式**，

- **防抖（debounce）**：多次高频操作优化为只执行一次。使用场景：防止重复提交，用户输入时，只在完成后校验或者搜索

```js
function debounce(fn, wait, immediate){
  let timer = null;

  return function(...args){
    let context = this;
    if(immediate && !timer) fn.apply(context, args);

    if(timer) clearTimeout(timer)

    timer = setTimeout(()=>{
      timer = null;
      !immediate ? fn.apply(context, args) : null
    }, wait)
  }
}
```

- **节流（throttle）**: 每隔一段时间后执行一次，也就是降低频率，将高频操作优化成低频操作，通常使用唱尽为 滚动条事件 或者 resize 事件，通过每个 100~500ms 执行一次即可

```js
function throttle(fn, wait, immediate) {
  let timer = null;
  let previous = 0;

  return function(...args){
    let now = new Date();
    let context = this;
    let interval = wait - (now - previous);

    if(immediate && !timer) fn.apply(context, args);

    if(interval <= 0){
      clearTimeout(timer);
      timer = null;
      fn.apply(context, args);
      previous = new Date();
    } else if (!timer){
      timer = setTimeout(()=>{
        clearTimeout(timer);
        timer = null;
        !immediate ? fn.apply(context, args) : null;
        previous = new Date()
      }, wait)
    }
  }
}
```

## 17. 函数执行改变 this

谁调用了函数，`this` 就指向谁

- `obj.fn()`, this 为 obj
- `fn()`, 这里可以补全为 `window.fn()`, 因此 this 为 window

但是这种机制把并不能完全满足我们的业务要求，因此提供了三种方式可以手动修改 this 指向

- `call: fn.call(target, 1, 2)`
- `apply: fn.apply(target, [1,2])`
- `bind: fn.bind(target)(1,2)`

## 18. ES6

ES6 是指 准确的来说时指 ECMA2015 的版本，我们通常说的 ES6 一般泛指所有的新的ECMA标准

- `let\const` 与块级作用域
  - `let\const`: 块级作用于、不存在变量提升，存在暂时性死区，不允许重复生命
  - `const`: 声明常量，无法修改

- 解构赋值
  - 数组的解构
  ```js
  const arr = [100,200,300]
  // 对应位置对应赋值
  const [a,b,c] = arr // a=100 b=200 c=300
  // 获取特定位置
  const [,,e] = arr //300 
  // 获取数组剩余全部，注意...赋值只能在最后解构的最后位置使用
  const [f,...rest] = arr; // f=100; rest = [200,300]
  // 解构变量数量小于或者大于数组长度
  const [foo] = arr; // 小于时从前往后提取，多出的不提取
  const [z,x,c,more]; // more为undefined
  // 设置默认值
  const [default = 200] = arr; // 如果对应的位置没有值，default=200，对应位置有
  ```
  - 对象的解构
  ```js
  const obj = {name:'abc', age: 18}

  const {name, age} = obj; // name = 'abc', age = 18

  // 当作用域中存在与解构对象键名重复的情况时，可以将解构出来的值重新赋值给另外一个变量
  const {name:myname, age} = obj; // myname = 'abc' age = 18

  ```

- 模板字符串
  - 支持多行字符串
  - 通过插值表达式的方式（任何js语句），嵌入对应的值
  - 带标签的模板字符串
  ```js
  const name = 'zxc';
  const gender = true;
  /**
  * strings 为模板字符串在变量位置分割成的所有字符串片段组成的数组
  * name 为模板字符串第一个变量
  * gender 为模板字符串的第二个变量
  * ... 以此类推，也可使用剩余语法，将所有的参数组合成一个按变量位置排序的数组
  */
  function myTagFunc(strings, name, gender){
      console.log(strings, name, gender)
      return 123
  }
  
  const result = myTagFunc`hey, ${name} is a ${gender}`  
  ```

- 字符串扩展方法
  - `includes`: 是否包含给定字符串
  - `startsWith`: 是否以指定字符串开头
  - `endsWith`: 是否以给定字符串结尾

- 剩余参数（Rest Parameter）

```js
// 只能使用在函数参数的最后的位置
function(...rest){
    console.log(rest) // rest为所有参数组成的数组
}
```
- 展开运算符（Spread Syntax)

```js
const arr = [1,2,3];
console.log(...arr); // 1,2,3
```

- 箭头函数 与 this

```js
// 箭头函数不会改变this指向
const person = {
    name: 'sam',
    sayHi: () => {
        console.log(this.name)
    }
}

person.sayHi()   // undefined
```

- 对象字面量的增强

如果字面量和对象键名一致，就可以省略赋值

```js
const bar = 123;

const obj = {
    foo: '111',
    bar,
    [Math.random()]: '222' // 计算属性名，[]中的计算结果作为对象的键名
}

```

- 对象扩展方法

- `Object.assgin()`
将多个源对象中的属性复制到目标对象中，如果存在相同属性，目标对象中的属性值会被源对象中的属性值覆盖。该方法的返回值为目标对象
```js
Object.assign(target, source1, source2, source3, source4,....)
```

- `Object.is()`
判断两个值是否相等。可判断两个NaN是否相等。

- `Object.defineProperty()`

- `Proxy`
  代理，对对象进行个性化的定制
  vs `Object.defineProperty()`
  - defineProperty 只能监听属性的读写
  - Proxy 能够监视到更多的对象的操作，例如 delete 操作，in 等
  - Proxy 更好的支持数组对象的监视
  - Proxy 是以非侵入的方式监管了对象的读写
```js
const person = {
    name:'zxc',
    age: 18
}

const personProxy = new Proxy(person, {
    // target 为代理目标对象
    // property 为要访问的目标属性
    // value 为要设置的值
    get(target, property){
        console.log(target, property);
        return property in target ? target[property] : undefined
    }
    set(target, property, value){
        console.log(target, property, value)
    }
})
personProxy.country = 'China'

console.log(personProxy.name) // zxc'
console.log(personProxy.gender) // undefined
```

- `Reflect` 统一对象操作的API，静态类，不能通过new对象实例化
  `Reflect` 内部封装了一系列对对象的底层操作
  `Reflect` 成员方法就是`Proxy`处理对象的默认实现

- Promise 对象

解决了回调地狱的问题

- 生成器 Genarator
  - `yield`: 暂停代码
  - `next()`: 继续执行代码
  ```js
  function* helloWorld() {
    yield 'hello';
    yield 'world';
    return 'ending';
  }

  const generator = helloWorld();

  generator.next()  // { value: 'hello', done: false }

  generator.next()  // { value: 'world', done: false }

  generator.next()  // { value: 'ending', done: true }

  generator.next()  // { value: undefined, done: true }
  ```
- async/await

是generator的语法糖， babel中是基于promise实现。

- 类的继承

extends\super

- Set数据结构

Set数据集合中的每一个值都是独一无二的，即Set数据集合中不存在重复的值

- Map数据结构

Map对象的键名可以为任何值，而普通对象的键名只能是`string`。

- Symbol 全新的原始数据结构

表示一个独一无二的值，从ES2015后，symbol可以作为对象的键名。最主要的作用就是为对象添加一个独一无二的属性名。

- `symbol`作为属性名，通过`for...in、Object.keys()、JSON.stringify()`方法，是无法获取到的。可以通过`Object.getOwnPropertySymbol()`方法获取
- 创建同一个`symbol`，可以使用`Symbol.for('key')`，key相同时，创建的`symbol`数据就是相等的

- for...of 循环

作为遍历所有数据结构（可迭代）的一种方式。默认可遍历数组，Set对象，Map对象等，对于对象来说，只要手动实现iterator内部逻辑即可。

- `Itrable` 接口
```js

// 实现可迭代器，Iterator
const obj = {
    store : ['foo', 'bar', 'baz'],
    [Symbol.iterator]: function(){
        let index = 0;
        const _this = this;
        return {
            next: function(){
                return {
                    value: _this.store[index],
                    done: index++ >= self.store.length
                }
            }
        }
    }
}

for(let i of obj){
    console.log(obj)
}

// 迭代器的作用
// 对外部提供统一的接口，使外部不用关心内部的数据接口。对于js的语言来说，内部实现了统一的迭代器的接口，我们在使用的时候只需要关心实现迭代器的内部逻辑即可。

```

## 19. AST（Abstract Syntax Tree）抽象语法树

**抽象语法树 (Abstract Syntax Tree)**，是将代码逐字母解析成 树状对象 的形式。这是语言之间的转换、代码语法检查，代码风格检查，代码格式化，代码高亮，代码错误提示，代码自动补全等等的基础。

## 20. babel编译原理
- babylon 将 ES6/ES7 代码解析成 AST
- Babel-traverse 对 AST 进行遍历转义，得到新的 AST
- 新 AST 通过 Babel-generator 转换成 ES5

## 21. 数组
- `map`: 遍历数组，返回回调函数返回值组成的新数组
- `forEach`: 无法`break`, 可以用 `try...catch` 中 `throw new Error`来停止
- `filter`: 过滤，返回符合条件的值组成的新数组
- `some`: 有一项满足，返回 true
- `every`: 每一项满足，返回 true
- `join`: 通过指定连接符生成字符串
- `push / pop`: 末尾推入和弹出，改变原数组。push 返回数组长度，pop 返回原数组最后一项
- `unshift / shift`: 头部推入和弹出，改变原数组，UNshift 返回数组长度，shift 返回数组原数组的第一项
- `sort(fn) / reverse`: 排序 和 翻转，改变原数组
- `concat`: 连接数组，不影响原数组，浅拷贝
- `slice(start, end)`: 返回截断后的数组，不改变原数组
- `splice(start, num, value...)`: 返回删除元素组成的数组，value为插入项，改变原数组
- `indexOf / lastIndexOf(value, fromIndex)`: 查找数组项，返回对应的下标
- `reduce / reduceRight(fn(prev, cur), defaultPrev)`: 累加，prev 为上次执行的函数的 return 值，cur 为当前值
  - 当传入 defaultPrev 是，从第一项开始
  - 当未传入时，defaultPrev 默认为数组的第一项，从第二项开始
-  