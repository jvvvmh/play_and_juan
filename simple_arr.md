

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

