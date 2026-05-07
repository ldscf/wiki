---
source_title: Leetcode - 015 - 3Sum
categories:
- Develop
- Problem-Solving
last_modified: '2024-05-15T05:25:04Z'
---
15. 3Sum

Medium

Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.

Notice that the solution set must not contain duplicate triplets.
```
 Example 1:
```

  Input: nums = [-1,0,1,2,-1,-4]

  Output: [[-1,-1,2],[-1,0,1]]

  Explanation: 

  nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0.

  nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.

  nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0.

  The distinct triplets are [-1,0,1] and [-1,-1,2].

  Notice that the order of the output and the order of the triplets does not matter.
 
```
 Example 2:
```

  Input: nums = [0,1,1]

  Output: []

  Explanation: The only possible triplet does not sum up to 0.
 
```
 Example 3:
```

  Input: nums = [0,0,0]

  Output: [[0,0,0]]

  Explanation: The only possible triplet sums up to 0.
```
 Constraints:
```

    3 <= nums.length <= 3000

    -105 <= nums[i] <= 105

Accepted 3.5M, Submissions 10.2M, Acceptance Rate 34.5%

#### Solution
1. 先判断数组中是否有三个 0，如果有，结果中先加入: [0,0,0]
1. 对数组去重，按小大排序(建议保存相应原位置，若有重复元素则保存其最小位置)。在去重排序过程中，如果有相同元素，若其“和 * (-1)”也在数组中，则加入结果

# (第一个元素)遍历数组，遍历剩余的数组为内层循环。取首尾为第二、三个元素，当首位置=尾位置时退出内层循环

> 
- 如果三个元素之和等于零，加入结果，首位置++，尾位置--
- 若和大于零，首位置++；若首位置元素为非负，则退出第二层遍历
- 若和小于零，尾位置--；若尾位置元素为非正，则退出第二层遍历

#### Java
