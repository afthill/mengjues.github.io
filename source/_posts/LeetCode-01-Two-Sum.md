title: 'LeetCode 01: Two Sum'
date: 2016-10-25 00:28:29
tags:
- LeetCode
categories:
- 算法
---

### Question:

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution.

Example:
```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

### Resolution:

我开始用到的暴力破解法：

```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        for i, num1 in enumerate(nums):
            for j, num2 in enumerate(nums[i + 1:]):
                if num1 + num2 == target:
                    return [i, i + j + 1]
                    
        return None
```        

时间复杂度：`O(N^2)`，空间复杂度为：`O(1)`

### Discuss

看了方案，有一个更好的利用额外的一个字典存储已经遍历的值，做到一次遍历就可以求到解，这里使用的技巧是字典的查询操作是`O（1）`的，用来代替我上面为了找到第二个值的`O（N）`遍历，很巧妙，想法很好。不过这套方案，空间复杂度是`O（N）`，用空间优化了时间，很棒。

```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        number_dict = dict()
        for index, value in enumerate(nums):
            if target - value in number_dict:
                return [number_dict[target-value] , index]
            number_dict[value] = index
        return [-1, -1]
```
