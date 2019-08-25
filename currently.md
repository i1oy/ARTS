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
