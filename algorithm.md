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
