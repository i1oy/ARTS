# ARTS

## A

### [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

题目：合并两个有序的链表，返回一个新的有序链表。

第一个版本的思路：比较两个链表当前位置数值的大小，取出较小节点的值加入到合并的链表中，
并将较小节点的位置指向该链表的下一个节点；重复**比较->合并->向后移动**的操作，
直至其中一个链表的节点指向null；
最后将另一个链表剩余的节点加入到合并链表的尾部。

```JavaScript
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
    let mergeList = null, curPos = null;
    while (l1 !== null && l2 !== null) {
        let minimum;
        if (l1.val < l2.val) {
            minimum = l1.val;
            l1 = l1.next;
        } else {
            minimum = l2.val;
            l2 = l2.next;
        }
        if (curPos === null) {
            curPos = new ListNode(minimum);
            mergeList = curPos;
        } else {
            curPos.next = new ListNode(minimum);
            curPos = curPos.next;
        }
    }
    let tail = l1 || l2;
    while (tail !== null) {
        if (curPos === null) {
            curPos = new ListNode(tail.val);
            mergeList = curPos;
        } else {
            curPos.next = new ListNode(tail.val);
            curPos = curPos.next;
        }
        tail = tail.next;
    }
    return mergeList;
};
```

在上述解法中，每次合并的时候会创建一个新的节点，这一操作是没有必要的；
另外一个问题是由于无法得知原链表中哪一个节点会是合并后的第一个节点，
在遍历的过程中，增加了一次当前待合并的节点是否是首个合并节点的判断。

改进版思路：

1. 直接取原来链表中的节点加入到合并链表中；
2. 在合并链表中插入一个默认的首节点，最后返回结果的时候则跳过合并链表的首节点。

```JavaScript
var mergeTwoLists = function(l1, l2) {
    if (!l1 || !l2) {
        return l1 === null ? l2 : l1;
    }

    let mergeList = {val: -1, next: null}, curPos = mergeList;
    while (l1 !== null && l2 !== null) {
        if (l1.val < l2.val) {
            curPos.next = l1;
            l1 = l1.next;
        } else {
            curPos.next = l2;
            l2 = l2.next;
        }
        curPos = curPos.next;
    }
    let tail = l1 || l2;
    curPos.next = tail;

    return mergeList.next;
};
```

## R

### [How JavaScript works: inside the V8 engine + 5 tips on how to write optimized code](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)

本文聚焦于深入理解谷歌 V8 引擎的内部原理。

### 概述

JavaScript 引擎是执行 JavaScript 代码的程序或解析器。
JavaScript 引擎可以是标准的解析器，或者是将 JavaScript 编译成某种形式字节码的 JIT 编译器。

主流的 JavaScript 引擎：

- V8，开源，由谷歌开发，开发语言 C++
- Rhino，开源，由 Mozilla Foundation 管理，开发语言 Java
- SpiderMonkey，第一个 JavaScript 引擎，最初为网景浏览器所使用，现在用于火狐浏览器
- JavaScriptCore，开源，由苹果为 Safari 开发
- KJS，KDE 的引擎，最初由 Harri Porten 开发，主要用于 KDE 项目的 Konqueror 浏览器
- Chakra(JScript9), IE 浏览器
- Chakra(JavaScript)，微软 Edge 浏览器
- Nashorn，开源代码，作为 OpenJDK 的一部分
- JerryScrip，主要用于IoT的轻量级引擎

### 为什么要创建 V8 引擎

V8 引擎不仅用在了 Chrome 浏览器之中，
同时也被做为 Node.js 运行时。

V8 最初的设计目的是为了提供 JavaScript 代码在浏览器中的执行速度。
为了获得速度上的优势， V8 将 JavaScript 代码转换成更高效的机器码而不是使用解析器。
与现代的 JavaScript 引擎（例如， SpiderMonkey 或 Rhino(Mozilla)）类似，
V8 通过实现一个 JIT 编译器将 JavaScript 代码转换成机器码。
其中最大的区别是 V8 不产生字节码或任何的中间代码。

### V8 曾经有两个编译器

在版本5.9之前， V8 引擎使用了两个编译器

- full-codegen，一个简单且快速的编译器，生成简单但相对慢的机器码
- Crankshaft，更加复杂的 JIT 优化编译器，生成高度优化的代码

V8 引擎内部使用了多个线程

- 主线程：获取代码，编译代码，执行
- 一个单独的线程用于编译，因此主线程可以继续执行，而前者则正在优化代码
- 分析器线程：分析运行时占用大量时间的方法，以便与 Crankshaft 可以优化它们
- 少量的垃圾回收线程：处理垃圾回收

首次执行 JavaScript 代码时， V8 利用 full-codegen 直接将语法分析后的 JavaScript 代码
转换成机器码；
当代码运行一段时间后，分析器线程收集到足够的数据，分析得出哪些方法应该被优化；
然后，在另一个线程中执行 Crankshaft 优化。
引擎将 JavaScript 的抽象语法书转换成名为 Hydrogen 的高级静态单赋值形式，
并尝试优化 Hydrogen 图。大多数的优化在这一层级完成。

### 内联

将被调用函数的函数体替换到调用位置，称之为内联。
这个简单的操作使得后续优化更有意义。

### 隐藏类

JavaScript 是基于原型的语言：没有类的概念，对象的创建是通过克隆过程实现的。
同时 JavaScript 也是动态编程语言，这意味着在实例化后，
能够容易地从对象中添加或删除属性。

大多数 JavaScript 解析器使用的是类似字典的结构（基于哈希函数）来存储对象属性值在内存中的位置。
这种结构使得检索属性值的计算要比非动态语言更加的耗时。
以 Java 为例，所有的对象属性值在编译之前就被固定下来，
在运行时不能添加或删除（当然，C#拥有动态类型，这是另一个话题）。
因此，各个对象的属性值以固定的偏移存储在连续的内存空间中。
通过偏移量（由属性的类型决定）可以快速的定位到属性值。
然而在 JavaScript 中，这是不可能的，因为在运行时属性类型会发生变化。

因为使用类似字典结构并不是非常高效的方式，
所以 V8 引擎使用了一个不同的方法：hidden classes(隐藏类)。

隐藏类的机制与 Java 中固定对象设计类似，不同的是它们是在运行时被创建的。

```JavaScript
function Point(x, y) {
    this.x = x;
    this.y = y;
}

var p1 = new Point(1, 2);
```

以代码为例，当 `new Point(1, 2)` 被调用时，
V8 会创建一个隐藏类 `C0`。
因为还没有属性被定义，因此 `C0` 是空的。

当执行到 `this.x = x;` 时， V8 会基于 `C0` 创建一个新的隐藏类 `C1`。
`C1` 描述了属性 x 在内存中的位置。

当执行到 `this.y = y;` 时，会重复这些操作。

尽量使用相同的顺序来初始化属性，这样隐藏类就能够被重复利用，提交效率。

### 内联缓存

V8 使用了**内联缓存**技术来优化动态类型语言。
内联缓存依赖于观察到的现象：对同一个方法的重复调用往往会发生在相同类型的对象。

具体的工作原理： V8 引擎维护了一份缓存，记录着最近方法调用中被传递参数的对象类型。
利用这些信息对将来要传递参数的类型进行假设。
如果 V8 能够作出良好的假设，就能绕过处理如何访问对象属性的过程，
而是直接使用之前查找到的对象隐藏类中的存储信息。

对于同一个隐藏类的同一个方法，经过两次成功的调用后，
V8 引擎就会忽略对隐藏类的查找而是直接通过在对象指针上增加属性偏移量的方式得到属性。
该方法将来所有的调用， V8 引擎都会假设隐藏类不会发生变化，
直接使用属性的偏移量从内存地址获取指定的属性。

### 编译到机器码

一旦 Hydrogen 图被优化，Crankshaft 将其转换为 Lithium 的较低级别表示。
大多数 Lithium 的实现都是针对架构的。
寄存器分配发生在这一级。

### 垃圾回收

V8 使用传统的标记-清理的方法来进行垃圾回收。
为了控制垃圾回收的成本、使得代码执行更稳定， V8 使用增量标记的方式。

### Ignition and TurboFan

在 2017 年的早些时候，伴随着 V8 5.9 的发布，引入了新的执行管道。
新的管道取得了更优的性能提升和显著的内存节省。

新的管道建立在解释器 Ignition 和最新的优化器 TurboFan 之上。

自 V8 5.9 发布后， full-codegen 和 Crankshaft 就不再使用。

### 如何编写更优的代码

1. 使用相同的顺序实例化对象属性；
2. 避免动态添加属性，在构造函数中分配所有的属性；
3. 重复执行相同方法的代码将比仅执行一次不同方法的代码运行得更快；
4. 避免使用稀疏数组，稀疏数组可能是一开始创建产生的，也可能是删除元素使之变为稀疏；
5. 标记值

    V8 使用 32 位来表示对象和数字。其中使用了一个比特位来区分对象和整型数字；
    整型数被称为 SMI(SMall Integer)，因为只有 31 位。
    如果一个数字值大于 31 位，那么 V8 将对它装箱，
    将其换成 double 类型，并创建一个新的对象来存放这个数值。
    因此，尽可能使用 31 位带符号的数值，避免昂贵的装箱操作。
