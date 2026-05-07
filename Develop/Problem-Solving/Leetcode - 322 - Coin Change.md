---
source_title: Leetcode - 322 - Coin Change
categories:
- Develop
- Problem-Solving
last_modified: '2024-05-14T02:14:34Z'
---
322. Coin Change

Medium

You are given an integer array coins representing coins of different denominations and an integer amount representing a total amount of money.

Return the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.

You may assume that you have an infinite number of each kind of coin.
```
 *Example 1:
 Input: coins = [1,2,5], amount = 11
 Output: 3
 Explanation: 11 = 5 + 5 + 1
```

 
```
 Example 2:
 Input: coins = [2], amount = 3
 Output: -1
```

 
```
 Example 3:
 Input: coins = [1], amount = 0
 Output: 0
```

 
```
 Constraints:
```

     1 <= coins.length <= 12

     1 <= coins[i] <= 231 - 1

     0 <= amount <= 104*

Accepted 1.8M, Submissions 4M, Acceptance Rate 44.0%

### Solution

The solution of the original problem consists of the optimal solution of the subproblem. To conform to the "optimal substructure", the subproblems must be independent of each other. 
* When coin=1, dp[6] = 1 + the optimal solution of dp[5].
* When coin=2, dp[6] = 1 + dp[4].
* When coin=5, dp[6] = 1 + dp[1].

After iterating through all coins(1, 2, 5), the optimal solution of dp[6] is selected by min.
* the state transition equation: dp[i] = min(dp[i], 1 + dp[i-coin])

#### Java
```
 *public class lc322 {
```

     public static int coinChange(int[] coins, int amount) {

         int[] dp = new int[amount + 1];

         Arrays.fill(dp, amount + 1);

         dp[0] = 0;
 
         // coin list - <<

         HashMap<Integer, ArrayList<Integer>> exp1 = new HashMap();

         ArrayList<Integer> l1;

         exp1.put(0, new ArrayList<>());

         // coin list - >>
 
         for (int i=1; i<=amount; i++) {

             for (int c : coins) {

                 if (i >= c) {

                     if (dp[i] > dp[i-c]+1) {

                         dp[i] = dp[i-c]+1;
 
                         // coin list - <<

                         l1 = new ArrayList<>();

                         l1.add(c);

                         l1.addAll(exp1.get(i-c));

                         exp1.put(i, l1);

                         // coin list - >>
 
                     }

                 }

             }

         }
 
         // coin list - <<

         // System.out.println(exp1.toString());

         System.out.println(exp1.get(amount));

         // coin list - >>
 
         if (dp[amount] > amount) {

             return -1;

         } else {

             return dp[amount];

         }

     }

     public static void main(String[] args) {

         int[] coin1 = {1, 2, 5};

         int amount = 11;
 
```
 //        int[] coin1 = {2};
 //        int amount = 3;
 //
 //        int[] coin1 = {1};
 //        int amount = 0;
```

 
         System.out.println(coinChange(coin1, amount));

     }
```
 }*
```
