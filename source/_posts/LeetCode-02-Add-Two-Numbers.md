title: 'LeetCode 02: Add Two Numbers'
date: 2016-10-25 17:31:02
tags:
- LeetCode
categories:
- 算法
---

### Question:

You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
```

### My Resolution:

```
# Definition for singly-linked list.
#class ListNode(object):
#    def __init__(self, x):
#        self.val = x
#        self.next = None
class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        ret = l1
        while True:
            l1.val = l1.val + l2.val

            if l1.next and l2.next:
                l1 = l1.next
                l2 = l2.next
                continue
            if l1.next and (not l2.next):
                l1 = l1.next
                l2 = ListNode(0)
            if not l1.next and l2.next:
                l1.next = l2.next
                l2 = ListNode(0)
            if (not l1.next) and (not l2.next):
                carry_bit = False
                orig_ret = ret
                while True:
                    if carry_bit:
                        ret.val += 1
                    
                    if ret.val >= 10:
                        ret.val = ret.val % 10
                        carry_bit = True
                    else:
                        carry_bit = False
                        
                    if not ret.next:
                        if carry_bit:
                            ret.next = ListNode(0)
                            ret = ret.next
                        else:
                            return orig_ret
                    else:
                        ret = ret.next
                    
```

### Discussion

这次好像是一个中等难度的题，依然是暴力解法，首先把所有的链表都加起来。然后再处理
一次需要进位的问题。这个地方有个坑就是，它没有说明哪里链表是否相等，我开始只用简
单的相等长的链表的相加，后来提交时才发现错了。