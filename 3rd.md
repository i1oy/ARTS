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
