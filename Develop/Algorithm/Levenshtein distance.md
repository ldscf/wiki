---
source_title: Levenshtein distance
categories:
- Algorithm
last_modified: '2025-05-29T09:00:58Z'
---
Levenshtein Distance 是 MED(Minimum Edit Distance, 最小编辑距离)的一种，由俄罗斯数学家弗拉基米尔·莱文斯坦(Vladimir Levenshtein)在 1965 年提出，因此也被称为 Levenshtein Distance(莱文斯坦距离)。

Levenshtein Distance 指两个字串之间，由一个转换成另一个所需的最少编辑操作次数，允许的编辑操作包括：
1. Substitution: 将其中一个字符替换成另一个字符
1. Insertion: 插入一个字符
1. Deletion: 删除一个字符

在信息论、语言学和计算机科学领域，Levenshtein Distance 是用来度量两个序列相似程度的指标。例如，编辑距离可以指在两个单词之间，由其中一个单词转换为另一个单词所需要的最少单字符编辑操作次数。

如：ld[hello, hell] = 1(插入"o"), ld[sit, seat] = 2, ld[sea, ate] = 3

#### 应用场景
- 拼写检查

采用数据对齐的方式解决数据不完全一致问题，选取匹配度比较高(LD值比较小)的数据。如解决地址信息填写不规范问题
- 数据匹配

词典应用的拼写提示。例如输入了 throwab，就能提示出 throwable。遍历 t 开头的单词库，寻找匹配度比较高(LD值比较小)的单词进行提示
- 匹配侦测

例如可以把匹配度高于某一个阈值的代码、文章初筛出来以便于进一步确认是否抄袭

#### Algorithm
- If the characters at the corresponding positions of the two strings match, the cell value remains the same as the value in the previous row (diagonal cell).
- If the characters don't match, choose the minimum:
  - Deletion: Add 1 to the value in the cell above (deletion of the character from the first string).
  - Insertion: Add 1 to the value in the cell to the left (insertion of the character from the second string).
  - Substitution: Add 1 to the value in the diagonally preceding cell (substitution of the character in the first string).

#### Time Complexity

The Levenshtein distance algorithm implemented with dynamic programming has a time complexity of O(mn), where:
- m is the length of the first string.
- n is the length of the second string.

This means the running time of the algorithm grows proportionally to the product of the string lengths. 

Here's why:
1. The algorithm uses a 2D DP table with dimensions (m+1) x (n+1).
2. It iterates through each cell of this table, performing constant-time operations like comparisons and minimum value calculations.
3. The total number of cells to be filled is (m+1) * (n+1), which translates to O(mn) complexity.

Therefore, the time it takes to calculate the Levenshtein distance between two strings increases linearly with the combined length of both strings.

#### Sample

| LD | i | 0 | 1 | 2 | 3 |
| j | 0 | a | t | e |
| 0 | 0 | 0 | 1 | 2 | 3 |
| 1 | s | 1 | 1 | 2 | 3 |
| 2 | e | 2 | 2 | 2 | 2 |
| 3 | a | 3 | 2 | 3 | 3 |

### Leetcode
72. Edit Distance

Medium

Given two strings word1 and word2, return the minimum number of operations required to convert word1 to word2.

You have the following three operations permitted on a word:
- Insert a character
- Delete a character
- Replace a character
 ```
Example 1:
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
Example 2:
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
Constraints:
 0 <= word1.length, word2.length <= 500
 word1 and word2 consist of lowercase English letters.
```

Accepted 874.7K Submissions 1.5M Acceptance Rate 56.7%

### Code
```
 ld(x, y) = min -> ld(x-1, y)+1 OR ld(x, y-1)+1 OR ld(x-1, y-1)+[0|1]
 [0|1]: if x[-1] = y[-1], then 0 else 1
```

#### Python
 ```
// Levenshtein Distance, recursion
// Less efficient because there are multiple double counts
def ld(str1, str2):
    m = len(str1)
    n = len(str2)
    if m == 0: return n
    if n == 0: return m
    if str1 == str2: return 0
    d = 0 if str1[-1] == str2[-1] else 1
    return min(ld(str1, str2[:-1]) + 1, ld(str1[:-1], str2) + 1, ld(str1[:-1], str2[:-1]) + d)
```

#### Java
 ```
// Levenshtein Distance, m * n array
// The space used is larger
public static int levenshtein2 (String str1, String str2) {
    if (isnull(str1)) return str2.length();
    if (isnull(str2)) return str1.length();
    if (str1.equals(str2)) return 0;
    int m, n, d, i, j;
    m = str1.length();
    n = str2.length();
    int[][] dp = new int[m+1][n+1];
    for (i = 0; i<=m; i++) dp[i][0] = i;
    for (j = 1; j<=n; j++) dp[0][j] = j;
    for (i = 1; i<=m; i++) {
        for (j = 1; j<=n; j++) {
            d = (str1.charAt(i-1) == str2.charAt(j-1)) ? 0 : 1;
            dp[i][j] = min(dp[i-1][j-1]+d, min(dp[i][j-1]+1, dp[i-1][j]+1));
        }
    }
    return dp[m][n];
}
// Levenshtein Distance, min(m, n) * 2 array
public static int levenshtein (String str1, String str2) {
    if (isnull(str1)) return str2.length();
    if (isnull(str2)) return str1.length();
    if (str1.equals(str2)) return 0;
    // less space used - <<
    if (str1.length() > str2.length()) return levenshtein(str2, str1);
    // less space used - >>
    int m, n, d, i, j;
    m = str1.length();
    n = str2.length();
    int[][] dp = new int[m+1][2];
    for (i = 0; i<=m; i++) dp[i][0] = i;
    dp[0][1] = 1;
    for (j = 1; j<=n; j++) {
        for (i = 1; i<=m; i++) {
            d = (str1.charAt(i-1) == str2.charAt(j-1)) ? 0 : 1;
            dp[i][1] = min(dp[i-1][0]+d, min(dp[i][0]+1, dp[i-1][1]+1));
        }
        for (i = 0; i<=m; i++) {
            dp[i][0] = dp[i][1];
        }
        dp[0][1] = dp[0][0] + 1;
    }
    return dp[m][1];
}
```
