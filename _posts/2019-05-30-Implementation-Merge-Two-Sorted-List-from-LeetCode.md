---
layout: post
title: "Implementation <Merge Two Sorted List> from LeetCode"
date: 2019-05-30
tags: [Data Structure]
excerpt: "Merge Two Sorted List"
comments: true

---

### Introduction

Recently, I didn't write a code. Totally at all. But, I've found a kind of some recruiting image that seems funny. It was bad timing, because, the moment i saw that image, I had just one day to the apply. It's a little bit rash, anyway I've applied, and passed, got a coding test letter.

I need to be ready for coding test right now, ultimately that makes me do **LeetCode.**

### Problem Description

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

**Example:**

```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```

### How To Solve?

There is a plenty easy way to solve such this problem. 

Just imagine the structure of data. **Two Sorted Linked List**. As mentioned, it seems to have a kind of class like ListNode.

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
```

Then we can make a singly-linked list like below.

```python
l1 = ListNode(1)
l1.next = ListNode(2)
l1.next.next = ListNode(4)

l2 = ListNode(1)
l2.next = ListNode(3)
l2.next.next = ListNode(4)
```

Now, we have to write **def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode**. I will use recursive way to solve this problem is easy. If you want more easier way, just convert Linked List to Array we  are used to. Look into the below.

```python
def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
    if l1 is None:
        return l2
    if l2 is None:
        return l1
```

if one of the ListNode is None, then return oposite ListNode.

```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        if l1 is None:
            return l2
        if l2 is None:
            return l1

        if l1.val < l2.val:
            l1.next = self.mergeTwoLists(l1.next, l2)
            return l1
        else:
            l2.next = self.mergeTwoLists(l1, l2.next)
            return l2
```

Given test case is L1 [1, 2, 4] | L2 [1, 3, 4]. 

Look into process of function below or you can see through the debugger.

- 1 < 1, So turn into the else statement.
  - l2.next = self.mergeTwoLists(l1, l2.next)
    - 1 < 3 (In current status, l2.next is 3), So Turn into the if statement.
      - l1.next = self.mergeTwoLists(l1.next, l2)
        - ...

Repeat calling the method until the l1 or l2 is None.