



#### [644. Maximum Average Subarray II](https://leetcode.cn/problems/maximum-average-subarray-ii/)

subarray length >= k of max avg. output avg with tol 1e-5

双指针失效了? Yes

k=2

6 （5   7.1  1）10

最后4个数字的平均数: 5.775, 其实应该带上6

----

二分: 尝试是否存在 avg>=m 且 长度>=k 的subarray

等价于减掉m后有没有人大于0

\m之后的数组 a[0] a[1] ... a[k-1]    a[k]

​                      i                                        j

sum(j) - sum(min_idx)

j往右移动一格, i也同时右移动. 他们两个的offset总是相同, 使得sum[j]总是减去sum[i]或者i之前的.

如果sum(i)小, 更新sum(min_idx)

**注意! min_idx 一开始必须为 -1 不能是0**

```python
class Solution:
    def findMaxAverage(self, nums: List[int], k: int) -> float:
        # max(sum(i, j)) where (j - i + 1) >= k
        # nums = [1,12,-5,-6,50,3], k = 4
        s = [nums[0]]
        for i in range(1, len(nums)):
            s.append(nums[i] + s[-1])
        # whether exist i, j s.t. j - i + 1 >= k and avg > m?
        # or sum(i\m include, j\m) >= 0
        # or sum(j\m) - sum(i\m not include) >= 0
        # i=0, j=k-1, sum(i\m)keep住最小值, j右移
        def isPositive(i, j, m):
            '''i not included'''
            return s[j] - (s[i] if i >=0 else 0) >= (j - i) * mid
            
        def checkExist(m):
            i = 0
            j = k - 1
            if s[j] >= m * k:
                return True
            curr_min = 0
            curr_min_idx = -1
            for j in range(k, len(nums)):
                if s[i] - (i + 1) * m < curr_min: # not include min: i
                    curr_min = s[i] - (i + 1) * m
                    curr_min_idx = i
                i += 1
                if isPositive(curr_min_idx, j, m):
                    return True
            return False

        l, r = min(nums), max(nums)
        while r - l >= 1e-5:
            mid = (l + r) / 2.0
            if checkExist(mid):
                l = mid
            else:
                r = mid
        return (l + r) / 2.0
```

