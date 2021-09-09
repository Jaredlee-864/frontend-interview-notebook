### 翻转字符串里面的单词

> 输入："the sky is blue" 输出："blue is sky the"
> 
> 输入："  hello world!  " 输出："world! hello" 解释：输入前后可以有空格，但输出时不需要
> 
> 输入："a good   example" 输出："example good a" 解释：两个单词中间有多个空格，输出只需要一个空格

- 解法一：正则 + JS API

```js
const reverseWord = function(s){
  return s.trim().replace(/\s+/g, '').split(' ').reserve().join()
}
```

- 解法二：双端队列（不使用API）：顾名思义，就是两端都可以进队的队列

```js
const reverseWord = function(s){
  let left = 0, right = s.length -1, word = '', queue = [];
  // 先处理两端的空白字符
  while(s.charAt(left) === ' ') left++;
  while(s.charAt(right) === ' ') right--;
  while(left <= right){
    let char = s.charAt(left)
    if (char === ' ' && word) {
      queue.unshift(word);
      word = ''
    } else if (char !== '') {
      word += char;
    }
    left++
  }
  queue.unshift(word);
  return queue.join(' ');
}
```

### 给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出所有滑动窗口里的最大值

示例：

```js
输入： nums = [1,3,-1,-3,5,3,6,7] 和 k = 3
输出：[3,3,5,5,6,7]
```
提示：
[1 3 -1] -3 5 3 6 7 最大值：3
1 3 [-1 -3 5] 3 6 7 最大值：5

- 解法一：暴力解法（循环）

```js
const maxSlidingWindow = function (nums, k) {
  if(k === 1) return nums;
  const result = [];
  const arr = [];
  nums.forEach((num, index) => {
    arr.push(num);
    if (index >= k - 1) {
      result.push(Math.max(...arr));
      arr.shift();
    }
  });
  return result;
};
```

- 解法二：双端队列

```js
const maxSlidingWindow = function(nums, k){
  const queue = []
  const result = [];
  for (let i = 0; i < num.length; i++){
    // 处理 queue
    if(i - queue[0] >= k){
      queue.shift()
    }
    while(nums[queue[queue.length - 1]] <= nums[i]){
      queue.pop()
    }
    queue.push(i);
    if(i >= k -1){
      result.push(queue[0])
    }
  }
  return result;
}
```


### 手写 - 实现一个寄生组合继承

```js

function Parent(name){
  this.name = name;
  this.say = () => {
    console.log("This is Parent")
  }
}

Parent.prototype.play = () => {
  console.log("Parent Paly")
}

function Child (name) {
  Parent.call(this);
  this.name = name;
}

Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Children;
```

### 手写 - new 操作符

```js
function myNew(Ctor,...args){
  let obj = Object.create(Ctor.prototype);
  let res = Ctor.call(obj, ...args);
  if(res && (typeof res === "object" || typeof res === "function")){
    return res
  }
  return obj
}
// 用法如下：
function Person(name, age){
  this.name = name;
  this.age = age
}
Person.prototype.sayHello = () => {
  console.log("hello world")
}
let person = myNew(Person, 'frank', '28');
console.log(person.name, person.age)
person.sayHello()

```

### 手写 - 使用 setTimeout 模拟实现 setInterval

```js
function mySetInterval(fn, time = 1000) {
  let timer = null, isClear = false;
  function interval(){
    if(isClear){
      isClear = false;
      clearTimeout(timer);
      return;
    }
    fn();
    timer = setTimeout(interval, time);
  }
  timer = setTimeout(interval, time)
  return () => {
    isClear = true
  }
}
```

### 手写 - 发布订阅模式

```js

class EventBus {
  constructor() {
    this.events = {};
  }
  // 订阅
  on(type, callback) {
    if (!this.events[type]) {
      this.events[type] = [callback];
    } else {
      this.events[type].push(callback);
    }
  }
  // 触发通知
  emit(type, ...rest) {
    this.events[type] && this.events[type].forEach((cb) => cb.apply(this, rest));
  }
  // 删除事件
  off(type, callback) {
    if (!this.events[type]) return;
    this.events[type] = this.events[type].filter((cb) => cb !== callback);
  }
  // 一次
  once(type, callback) {
    function fn() {
      callback();
      this.off(type, fn);
    }
    this.on(type, fn);
  }
}

```

### 手写 - 将虚拟 DOM 转换为真实 DOM

```js
{
  tag: 'DIV',
  attrs:{
  id:'app'
  },
  children: [
    {
      tag: 'SPAN',
      children: [
        { tag: 'A', children: [] }
      ]
    },
    {
      tag: 'SPAN',
      children: [
        { tag: 'A', children: [] },
        { tag: 'A', children: [] }
      ]
    }
  ]
}
// 把上诉虚拟Dom转化成下方真实Dom
<div id="app">
  <span>
    <a></a>
  </span>
  <span>
    <a></a>
    <a></a>
  </span>
</div>

题解：
function createElm(vnode){
  if(typeof vnode === "number"){
    vnode = String(number)
  }
  if(typeof vnode === "string"){
    return document.createTextNode(vnode)
  }
  const el = document.createElement(vnode.tag);
  if(vnode.attrs){
    Object.keys(vnode.attrs).forEach(key =>{
      const value = vnode.attrs[key];
      el.setAttribute(key, value)
    })
  }
  vnode.children && vnode.children.forEach(child => el.appendChild(cteateElm(child)));
  return dom;
}
```