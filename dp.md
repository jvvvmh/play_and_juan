

#### [77. Combinations](https://leetcode.cn/problems/combinations/)

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        x = [[] for _ in range(k + 1)]
        x[0] = [0]
        for i in range(1, n + 1):
            if i < n - k:
                for j in range(min(i, k), -1, -1):
                    # 从上来+0
                    # x[j] = [tmp << 1 for tmp in x[j]]
                    for idx in range(len(x[j])):
                        x[j][idx] = (x[j][idx] << 1)
                    # 从左上来+1
                    if j - 1 >= 0:
                        # x[j] += [(tmp << 1) + 1 for tmp in x[j - 1]]
                        for tmp in x[j - 1]:
                            x[j].append((tmp << 1) + 1)
            else:
                for j in range(k, (k - (n - i)) - 1, -1):
                    # 从上来+0
                    # x[j] = [tmp << 1 for tmp in x[j]]
                    for idx in range(len(x[j])):
                        x[j][idx] = (x[j][idx] << 1)
                    # 从左上来+1
                    if j - 1 >= 0:
                        # x[j] += [(tmp << 1) + 1 for tmp in x[j - 1]]
                        for tmp in x[j - 1]:
                            x[j].append((tmp << 1) + 1)
        res = []
        for tmp in x[-1]:
            res.append([i + 1 for i in range(n) if (tmp >> i) & 1])
        return res
```



#### [72. Edit Distance](https://leetcode.com/problems/edit-distance/)

```C++
class Solution {
public:
    int minDistance(string word1, string word2) {
        // dp[0][0] = 0 下标表示长度
        // 如果不相等, dp[i][j] = 1 + ?
        // (1) insert, dp[i][j - 1] if j - 1 == 0, then give i
        // (2) delete, dp[i - 1][j] if i - 1 == 0, then give j  
        // (3) replace, dp[i - 1][j - 1]

        int m = word1.size();
        int n = word2.size();
        vector<int> f(n + 1, 0);
        for (int j = 0; j <= n; ++j) {
            f[j] = j;
            
        }
        
        for (int i = 1; i <= m; ++i) {
            vector<int> g(n + 1, INT_MAX);
            g[0] = i;
            for (int j = 1; j <= n; ++j) {
                if (word1[i - 1] == word2[j - 1]) { // 注意这里是idx
                    g[j] = f[j - 1];
                } else {
                    g[j] = min(g[j], 1 + g[j - 1]);
                    g[j] = min(g[j], 1 + f[j]);
                    g[j] = min(g[j], 1 + f[j - 1]);
                }
            }
            f = move(g);
        }

        return f[n];
    }
};
```



#### [63. Unique Paths II](https://leetcode.cn/problems/unique-paths-ii/)

从左上到右下有多少种走法 (有障碍物)

```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        # dp[i][j] = dp[i - 1][j] + dp[i][j - 1] 如果他自己不是石头的话
        dp = []
        m, n = len(obstacleGrid), len(obstacleGrid[0])
        for j in range(n):
            if j == 0:
                dp.append(1 - obstacleGrid[0][0])
            else:
                dp.append((1 - obstacleGrid[0][j]) & dp[-1])
        for i in range(1, m):
            for j in range(n):
                if obstacleGrid[i][j] == 1:
                    dp[j] = 0
                else:
                    if j >= 1:
                        dp[j] = dp[j] + dp[j - 1]
        return dp[-1]
```



#### [62. Unique Paths](https://leetcode.cn/problems/unique-paths/)

从左上到右下有多少种走法

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        # dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
        # 1 1 1 1
        # 1 2 3 4
        # 1 3 6 10
        dp = [1] * n
        for i in range(m - 1):
            for j in range(n):
                if j == 0:
                    dp[j] = 1
                else:
                    dp[j] += dp[j - 1]
        return dp[-1]
```



#### [44. Wildcard Matching](https://leetcode.cn/problems/wildcard-matching/)

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        # (1) char
        # (2) * 如果用了它, s[i-1] j继续保留; 也可以不用
        dp = [[False] * (len(p) + 1) for _ in range(len(s) + 1)]
        dp[0][0] = True

        def isEqual(x, y):
            if p[y] == '?':
                return True
            else:
                return s[x] == p[y]

        for i in range(len(s) + 1):
            for j in range(len(p) + 1):
                if j == 0: continue
                # 更新dp[i][j]
                if p[j - 1] != '*':
                    if i >= 1 and isEqual(i - 1, j - 1):
                        dp[i][j] = dp[i - 1][j - 1]
                else:
                    # 可以使用 '*'
                    if i >= 1:
                        dp[i][j] |= dp[i - 1][j]
                    # 可以抛弃 '*'
                    dp[i][j] |= dp[i][j - 1]

        return dp[len(s)][len(p)]
```



#### [10. Regular Expression Matching](https://leetcode.cn/problems/regular-expression-matching/)

String s and a pattern p, implement regular expression matching with support for '.' and '*' where:

​        '.' Matches any single character.
​        '*' Matches zero or more of the preceding element.

长度为0的两个串match.

如果p是a*, 可能match s可能丢掉两

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        # regular -> f[i][j] = f[i - 1][j - 1]  / false
        # 
        # p[j] = *
        # p[j - 1] match s[i]: 可以不使用 a*, 也可以使用
        # 不match: 不使用a*

        # f[i][j] 表示 已经匹配了长度 i 和 j
        def charEqual(i, j):
            if i == -1:
                return False
            if p[j] == '.':
                return True
            return s[i] == p[j]

        f = [[False] * (len(p) + 1) for _ in range(len(s) + 1)]
        f[0][0] = True
        for x in range(len(s) + 1):
            for y in range(1, len(p) + 1): # pattern如果没有的话就只能是f[0][0]=True
                j = y - 1
                i = x - 1
                if p[j] == '*':
                    if charEqual(i, j - 1):
                        f[x][y] |= f[x - 1][y] # 这里用上
                    f[x][y] |= f[x][y - 2] # 不用
                else:
                    if charEqual(i, j):
                        f[x][y] = f[x - 1][y - 1]
            
        return f[len(s)][len(p)]
```



#### [53. Maximum Subarray](https://leetcode.cn/problems/maximum-subarray/)

Subarry with max sum.

```C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int res = nums[0], prev = 0;
        for (auto &i : nums) {
            prev = prev > 0 ? prev + i : i;
            res = max(res, prev);
        }
        return res;
    }
};
```

