---
layout: post
title: "Algorithm - PizzaBoxes"
date: 2019-09-26
tags: [Algorithm]
excerpt: "Solution using partitioning array."
comments: true
---

### Problem Solution

```python
test_1 = [
    [1, 2, 4, 6],
    [16, 9, 13, 11],
    [5, 10, 8, 15],
    [12, 14, 7, 3]
]

test_2 = [
    [1, 11, 25, 20, 23],
    [17, 2, 16, 21, 15],
    [10, 3, 12, 24, 22]
]


def pizza_box(test_case):
    # n => row, m => column
    n, m = len(test_case), len(test_case[0])

    total_sum = sum([sum(i) for i in test_case])
    row_max = [max(i) for i in test_case]
    columns = []

    for i in range(m):
        element = []
        for j in range(n):
            element.append(test_case[j][i])
            if len(element) + 1 == n:
                columns.append(element)

    columns_max = [max(i) for i in columns]

    total_max = []

    for i in row_max:
        total_max.append(i)

    for i in columns_max:
        total_max.append(i)

    return total_sum - sum(set(total_max))


print(pizza_box(test_1))
```

Approach of this solution is **partitioning array** row-based and column-based.

We assume we have total sum of elements of given array, Process is as follows (test_1):

- Partitioning array. 
  - column-based is [[1,16, 5, 12] … [6, 11, 15, 3]]
  - row-based is [[1, 2, 4, 6] … [12, 14, 7, 3]]
- Get set(max(column-based-arr) + max(row-based-arr))
- Answer = total-sum - sum(set(max(column-based-arr) + max(row-based-arr)))
