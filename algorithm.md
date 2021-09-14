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

### 手写-实现一个对象的 flatten 方法（阿里）

题目描述

```js
const obj = {
 a: {
        b: 1,
        c: 2,
        d: {e: 5}
    },
 b: [1, 3, {a: 2, b: 3}],
 c: 3
}

flatten(obj) 结果返回如下
// {
//  'a.b': 1,
//  'a.c': 2,
//  'a.d.e': 5,
//  'b[0]': 1,
//  'b[1]': 3,
//  'b[2].a': 2,
//  'b[2].b': 3
//   c: 3
// }

```
实现如下：
```js
function flatten(obj) {
  function isObj(val) {
    return typeof val === "object" && val !== null;
  }
  if (!isObj(obj)) return;
  let res = {};
  function flat(cur, delimiter = "") {
    if (Array.isArray(cur)) {
      cur.forEach((val, index) => {
        flat(val, `${delimiter}[${index}]`);
      });
    } else if (isObj(cur)) {
      for (let i in cur) {
        flat(cur[i], `${delimiter}${delimiter ? "." : ""}${i}`);
      }
    } else {
      res[delimiter] = cur;
    }
  }
  flat(obj);
  return res;
}

```

### 手写-判断括号字符串是否有效（小米）

题目描述：

```js

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

    左括号必须用相同类型的右括号闭合。
    左括号必须以正确的顺序闭合。

示例 1：

输入：s = "()"
输出：true

示例 2：

输入：s = "()[]{}"
输出：true

示例 3：

输入：s = "(]"
输出：false

```

代码实现：

```js
function isPairBrackets(str) {
  const pairObj = {
    "(": ")",
    "[": "]",
    "{": "}",
  };
  let stack = [];
  for (let i = 0; i < str.length; i++) {
    let cur = str[i];
    if (cur === "(" || cur === "[" || cur === "{") {
      stack.push(cur);
    } else if (cur === ")" || cur === "]" || cur === "}") {
      const key = stack.pop();
      if (cur !== pairObj[key]) return false;
    }
  }
  if (stack.length) return false;
  return true;
}
```

### 手写-查找数组公共前缀（美团）

题目描述：

```js
编写一个函数来查找字符串数组中的最长公共前缀。
如果不存在公共前缀，返回空字符串 ""。

示例 1：

输入：strs = ["flower","flow","flight"]
输出："fl"

示例 2：

输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```
代码实现：

```js
function maxPrefix(strs) {
  const str = strs[0];
  let index = 0,
    res = "";
  while (index < str.length) {
    for (let i = 0; i < strs.length; i++) {
      if (!strs[i][index] || strs[i][index] !== str[index]) {
        return res;
      }
    }
    res += str[index];
    index++;
  }
  return res;
}
```

### 手写-查找数组公共前缀（美团）

题目描述：

```js

给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。


示例 1:

输入: s = "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

示例 2:

输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

示例 3:

输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

示例 4:

输入: s = ""
输出: 0

```

代码实现：

```js
const lengthOfLongestSubstring = function (s) {
      if (s.length === 0) return 0;
      let left = 0,
        right = 1,
        max = 0;
      while (right <= s.length) {
        let lr = s.slice(left, right);
        const index = lr.indexOf(s[right]);
        if (index > -1) {
          left = index + left + 1;
        } else {
          lr = s.slice(left, right + 1);
          max = Math.max(max, lr.length);
        }
        right++;
      }
      return max;
    };
```

### 手写-如何找到数组中第一个没出现的最小正整数 怎么优化（字节）

题目描述：

```js

给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。
请你实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案。

示例 1：

输入：nums = [1,2,0]
输出：3

示例 2：

输入：nums = [3,4,-1,1]
输出：2

示例 3：

输入：nums = [7,8,9,11,12]
输出：1

```

代码实现：

```js

// 暴力穷举法 O(n^2) 的方法
function firstMissingPositive(nums) {
  let i = 0,
    res = 1;
  while (i <= nums.length) {
    if (nums[i] === res) {
      res++;
      i = 0;
    } else {
      i++;
    }
  }
  return res;
}

// 数据集合法 时间空间均为 O(n)
function firstMissingPositive(nums) {
  let set = new Set();
  res = 1;
  for (let i = 0; i <= nums.length; i++) {
    set.add(nums[i]);
  }
  for (; res < set.size + 1; res++) {
    if (!set.has(res)) {
      return res;
    }
  }
}

// 替换法 最终版 时间复杂度为 O(n) 并且只使用常数级别空间 TODO:
function firstMissingPositive(nums) {
    for (let i = 0; i < nums.length; i++) {
      while (
        nums[i] >= 1 &&
        nums[i] <= nums.length && // 对1~nums.length范围内的元素进行安排
        nums[nums[i] - 1] !== nums[i] // 已经出现在理想位置的，就不用交换
      ) {
        const temp = nums[nums[i] - 1]; // 交换
        nums[nums[i] - 1] = nums[i];
        nums[i] = temp;
      }
    }
    // 现在期待的是 [1,2,3,...]，如果遍历到不是放着该放的元素
    for (let i = 0; i < nums.length; i++) {
      if (nums[i] != i + 1) {
        return i + 1;
      }
    }
    return nums.length + 1; // 发现元素 1~nums.length 占满了数组，一个没缺
  }

```