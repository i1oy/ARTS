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
