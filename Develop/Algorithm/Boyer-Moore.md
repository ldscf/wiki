---
source_title: Boyer-Moore
categories:
- Algorithm
last_modified: '2025-10-23T01:11:20Z'
---
Boyer-Moore 投票算法（Boyer-Moore Majority Vote Algorithm）主要用于在一个序列中寻找“多数元素”（majority element），即出现次数超过一半的元素。以线性时间和常数空间的效率著称，因而在很多计算机科学和工程场景中都有广泛应用。

由 Robert S. Boyer 和 J Strother Moore 在 1981 年提出。

### 典型应用
1. 找出数组中的多数元素: 给定一个大小为 n 的数组，找出其中出现次数超过 n/2 的元素
1. 选举系统计票: 在选举或投票系统中，确定是否存在获得绝对多数票的候选人
1. 当某类数据异常值占大多数时，用于快速识别异常

### 复杂度
- 时间复杂度: O(n)
- 空间复杂度: O(1)

### Leetcode
169. Majority Element

Easy

Given an array nums of size n, return the majority element.

The majority element is the element that appears more than ⌊n / 2⌋ times. You may assume that the majority element always exists in the array.
 ```
Example 1:
Input: nums = [3,2,3]
Output: 3
Example 2:
Input: nums = [2,2,1,1,1,2,2]
Output: 2
Constraints:
    n == nums.length
    1 <= n <= 5 * 104
    -109 <= nums[i] <= 109
Follow-up: Could you solve the problem in linear time and in O(1) space?
```

Accepted 4,262,902/6.5M, Acceptance Rate 65.7%

#### Python
 ```
def majorityElement(n):
    candidate = None
    count = 0
    for k in n:
        if count == 0:
            candidate = k
        count += (1 if k == candidate else -1)
    if n.count(candidate) > len(n) // 2:
        return candidate
    else:
        return None
```
