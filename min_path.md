#### [64. Minimum Path Sum](https://leetcode.cn/problems/minimum-path-sum/)

Grid of non-negative numbers. Min path from top left to bottom right. $O(MN)$

```C++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        // DP: from where
        int m = grid.size(), n = grid[0].size();
        vector<int> dp (n, INT_MAX);
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 && j == 0) {
                    dp[j] = grid[i][j];
                } else {
                    dp[j] = min(dp[j], j - 1 >= 0 ? dp[j - 1]: INT_MAX / 2) + grid[i][j];
                }
            }
        }
        return dp[n - 1];
        /*
        int m = grid.size(), n = grid[0].size();
        int res = INT_MAX;
        vector< vector<int> > v (m, vector<int>(n, INT_MAX));
        queue< pair< pair<int, int>, int> > q; // ((i, j), cost )
        q.emplace(make_pair(0, 0), grid[0][0]);
        while (q.size()) {
            pair<pair<int, int>, int> h = q.front();
            q.pop();
            int x = h.first.first;
            int y = h.first.second;
            int cost = h.second;
            if (cost >= v[x][y]) {
                continue;
            }
            v[x][y] = cost;
            // down?
            if (x + 1 <= m - 1) {
                int nextCost = cost + grid[x + 1][y];
                q.emplace(make_pair(x + 1, y), nextCost);
            }
            // right?
            if (y + 1 <= n - 1) {
                int nextCost = cost + grid[x][y + 1];
                q.emplace(make_pair(x, y + 1), nextCost);
            }
        }
        return v[m - 1][n - 1];
        */
    }
};
```

