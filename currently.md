# ARTS

## A

### [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

题目：给出一个仅包含`(,),[,],{,}`的字符串，按照规则检查这个字符串：

- 各种类型的括号必须保持各自配对的关系；
- 括号之间的配对要保持正确的顺序；

思路：依此读取每个字符，遇到左侧括号压入到一个栈中，遇到右侧括号则与栈顶的括号进行匹配；
如果配对成功，则出栈并继续遍历字符串；
直到匹配失败或是遍历完成；
当字符串遍历至最后一个字符且栈为空时，判定整个字符串符合要求。

```JavaScript
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
    if (s.length % 2 === 1) return false;
    const left = '([{';
    const right = ')]}';
    var bracket_stacks = [], i = 0;

    for (i = 0; i < s.length; i++) {
        const element = s[i];
        if (left.includes(element)) {
            bracket_stacks.push(element);
        } else if (right.includes(element)) {
            const index = right.indexOf(element);
            const stacks_length = bracket_stacks.length;
            if ( stacks_length > 0 && bracket_stacks[stacks_length - 1] === left[index] ) {
                bracket_stacks.pop()
            } else {
                break;
            }
        }
    }
    return bracket_stacks.length === 0 && i === s.length;
};
```

## R

### [How JavaScript works: an overview of the engine, the runtime, and the call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)

文章主要是对JavaScript的一些基本知识进行了概述，
包括JavaScript引擎、运行时和调用栈。
文章内容偏向基础，适合新手入门。

#### JavaScript引擎

谷歌是V8引擎是当前流行的引擎之一，
被应用在Chrome浏览器和Node.js。

V8的简化模型包括两个主要的组件：

- Memory Heap，内存分配发生的地方
- Call Stack，代码执行时堆栈帧的位置

#### 运行时

JavaScript开发者使用的Web API，比如DOM、AJAX、setTimeout等，
这些API是由浏览器提供的，而不是引擎。

#### 调用栈

JavaScript是单线程语言，只有一个调用栈。
调用栈是一种数据结构，记录程序执行的位置。

#### 并发与事件循环

当调用栈中的函数调用需要大量的时间处理时，就是导致线程阻塞，
浏览器无法处理其他代码、无法渲染页面，
最终引起页面崩溃。
这样的体验显然是不好。

解决方案就是**异步回调**。
