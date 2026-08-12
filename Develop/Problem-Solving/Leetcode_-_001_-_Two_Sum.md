---
source_title: Leetcode - 001 - Two Sum
categories:
- Develop
- Problem-Solving
last_modified: '2024-05-17T03:03:18Z'
---
1. Two Sum

Easy

Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.
```
 *Example 1:
```

 
```
 Input: nums = [2,7,11,15], target = 9
 Output: [0,1]
 Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```

 
```
 Example 2:
```

 
```
 Input: nums = [3,2,4], target = 6
 Output: [1,2]
```

 
```
 Example 3:
```

 
```
 Input: nums = [3,3], target = 6
 Output: [0,1]*
```
```
 *Constraints:
```

 
     2 <= nums.length <= 104

     -109 <= nums[i] <= 109

     -109 <= target <= 109

     Only one valid answer exists.*

Accepted 13.1M, Submissions 25M, Acceptance Rate 52.6%

### Solution

Traverse the array. Sum the values of the array with the keys that already exist in the map, and if the sum is the "target", output [get(key), index]. Otherwise, the value of the array is stored as a key, and the "index" is stored as a value in the map.

If the values of the array are duplicated, a relatively large "index" will be retained. If the key value exists, it will not be updated.

#### Java
```
 *class lc001 {
```

     public static int[] twoSum(int[] nums, int target) {

         Map<Integer, Integer> m1 = new HashMap<>();
 
         for (int i = 0; i < nums.length; i++) {

             if (m1.containsKey(target - nums[i]))

                 return new int[] {m1.get(target - nums[i]), i};

              if(!m1.containsKey(nums[i]))

                 m1.put(nums[i], i);

         }
 
         return new int[] {};

     }
 
     public static void main(String[] args) {

         int[] nums = {2,7,11,15};

         int target = 9;
 
```
 //        int[] nums = {3,2,4};
 //        int target = 6;
```

 
```
 //        int[] nums = {3,3};
 //        int target = 6;
```

 
```
 //        //special
 //        int[] nums = {3,2,3,5,15};
 //        int target = 8;
```

 
         System.out.println(Arrays.toString(twoSum(nums, target)));

     }
```
 }*
```
