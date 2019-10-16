# ARTS

## A

### [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

题目：移除有序数组中的重复元素，返回结果为移除后的数组长度。
要求在输入的数组上进行操作，不能原数组的引用指向其他新的数组（实际上这样操作也无法通过）。

最初的思路是：遍历数组，将当前元素与前一个元素进行比较;
如果两个元素相等，则使用splice方法移除重复元素，
后续元素自动向前移动，因此索引不需要变化，也能取到新的元素，继续进行比较；
如果两个元素不相等，则索引自增1，向后遍历。

```JavaScript
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function(nums) {
    let index = 0;
    while (index < nums.length) {
        if (nums[index - 1] === nums[index]) {
            nums.splice(index, 1);
        } else {
            index ++;
        }
    }
    return nums.length;
};
```

上述解法需要频繁地对数组元素进行移除操作，这样对效率是会有影响的。

另一种思路是不进行移除的动作，实际上只要将非重复的N个元素移动到数组的前N个位置上，最后返回数值N即可（此时，数组的长度保持原来的长度）。

```JavaScript
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function(nums) {
    if (nums.length === 0 || nums.length === 1) {
        return nums.length;
    }
    let pos = 0;
    for (let index = 1; index < nums.length; index ++) {
        if (nums[pos] !== nums[index]) {
            if (index - pos === 1) {
                pos = index;
            } else {
                pos ++;
                nums[pos] = nums[index];
            }
        }
    }
    return pos + 1;
};
```

## R

### [How JavaScript works: memory management + how to handle 4 common memory leaks](https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec)

本文聚焦于 JavaScript 的内存管理。

尽管绝大多数的高级语言提供了自动管理内存的特性，
但是理解语言的内存管理方式依然是有必要的。
这样可以帮助开发者以最小的代价正确地处理代码中与内存相关的问题。

#### 内存的生命周期

1. 分配内存
2. 使用内存
3. 释放内存

#### 什么是内存

在硬件层面，计算机内存由大量的触发器（Flip Flops）构成。

> 触发器是一种具有两种稳态的用于存储的组件，
> 可记录二进制数字信号“1”和“0”（来源：维基百科）

我们可以将计算机内存理解为一大块可以读写的字节空间。

编译器和操作系统共同工作帮助开发者处理大部分的内存管理。

**编译**代码时，编译器会解析原始数据类型，提前计算出所需的内存空间；
然后为程序在栈空间上分配相应的空间。

#### 动态分配

然而编译器并不总是能够在编译时计算出所需的内存空间，
某些情况下，必须在**运行时**向操作系统请求空间。

#### JavaScript中的内存分配

JavaScript在声明值时，自动分配内存。

#### 在JavaScript中使用内存

在JavaScript中使用内存时，实际上就是对内存的读写

#### 内存释放

大部分的内存管理问题就发生在这里。

最难的地方在于确定被分配的内存何时不再被需要。

高级编程语言会嵌入一个**垃圾回收器**来自动释放不再被需要的内存。
然而一般关于关于内存是否被需要的问题是不可判定的（无法通过一个算法解决）。

#### 垃圾回收

由于内存是否是不再被需要的问题是不可判定的，因此，垃圾回收的实现是存在局限的。

#### 常见的垃圾回收算法

1. 引用计数垃圾回收

    最简单，当一个对象的引用计数为零时，即执行回收操作。
    存在问题：无法处理循环引用的问题。

2. 标记清除算法

    (1).根节点：由垃圾回收器建立完整的根节点列表；
    (2).然后将所有根节点及其子节点标记为活跃状态；将根节点无法触达的标记为垃圾；
    (3).根据标记结果释放内存

    该算法解决了对象循环引用的问题。

    截止2012年，所有现代浏览器内置了标记清除式的垃圾回收器。

#### 内存泄露

内存泄露指的是程序曾经使用但不再需要的内存片段，
却没有返回到操作系统或可用内存池中。

#### 四种常见的JavaScript内存泄露

1. 全局变量

    由于JavaScript本身的问题，导致在代码中意外地创建了全局变量。
    这些全局变量无法通过垃圾回收器进行回收。

2. 被遗忘的定时器或是回调函数（不是特别明白？！）

    ```JavaScript
    var someResource = getData();
    setInterval(function() {
        var node = document.getElementById('Node');
        if(node) {
            // Do stuff with node and someResource.
            node.innerHTML = JSON.stringify(someResource));
        }
    }, 1000);
    ```

    上述的代码，初步理解为设置了定时器却没有将其停止，导致node和someResource都无法被回收。

    ```JavaScript
    var element = document.getElementById('button');

    function onClick(event) {
        element.innerHtml = 'text';
    }

    element.addEventListener('click', onClick);
    // Do stuff
    element.removeEventListener('click', onClick);
    element.parentNode.removeChild(element);
    // Now when element goes out of scope,
    // both element and onClick will be collected even in old browsers that don't
    // handle cycles well.
    ```

    老版本的 IE 是无法检测 DOM 节点与 JavaScript 代码之间的循环引用，会导致内存泄漏。
    如今，现代的浏览器（包括 IE 和 Microsoft Edge）使用了更先进的垃圾回收算法，
    已经可以正确检测和处理循环引用了。
    换言之，回收节点内存时，不必非要调用 removeEventListener 了。

3. 闭包

    ```JavaScript
    var theThing = null;
    var replaceThing = function () {
        var originalThing = theThing;
        var unused = function () {
            if (originalThing)
                console.log("hi");
        };
        theThing = {
            longStr: new Array(1000000).join('*'),
            someMethod: function () {
                console.log(someMessage);
            }
        };
        // Solution:
        // If you add `originalThing = null` here, there is no leak.
    };
    setInterval(replaceThing, 1000);
    ```

    上述代码做了一件事情：每次调用 replaceThing ，
    theThing 得到一个包含一个大数组和一个新闭包（someMethod）的新对象。
    同时，变量 unused 是一个引用 originalThing 的闭包（先前的 replaceThing 又调用了 theThing ）。
    思绪混乱了吗？
    最重要的事情是，闭包的作用域一旦创建，它们有同样的父级作用域，作用域是共享的。
    someMethod 可以通过 theThing 使用，someMethod 与 unused 分享闭包作用域，
    尽管 unused 从未使用，它引用的 originalThing 迫使它保留在内存中（防止被回收）。
    当这段代码反复运行，就会看到内存占用不断上升，垃圾回收器（GC）并无法降低内存占用。
    本质上，闭包的链表已经创建，每一个闭包作用域携带一个指向大数组的间接的引用，造成严重的内存泄漏。

    关于闭包内存泄漏的问题，参考了`Alon's Blog`的[翻译文章](https://jinlong.github.io/2016/05/01/4-Types-of-Memory-Leaks-in-JavaScript-and-How-to-Get-Rid-Of-Them/)。

4. DOM外引用

    如果在代码中保存了指向DOM节点的引用，
    那么对于同一个DOM节点就会存在至少两个引用：
    一个是在DOM树中，另一个是在代码的数据中。

    当你要移除这些DOM节点时，就要确保两个引用都不可访问。

    其中一个例子是：
    由于单元格是表的子节点，且单元格会保存指向父节点的引用。
    当你保存了一个指向表单元格的引用时，因此，整个数据表都会被保存在内存中。

## T

## S
