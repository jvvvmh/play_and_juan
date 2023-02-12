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

