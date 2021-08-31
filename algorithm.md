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
