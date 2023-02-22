



#### [69. Sqrt(x)](https://leetcode.cn/problems/sqrtx/) rounded down

1. $\sqrt{x} = \exp^{0.5 \ln(x)}$
2. binary search $O(\log(n))$
3. newton $y=x^2 - C$ 初始在右边. $O( \log(n) )$ 二次收敛

```python
class Solution {
public:
    int mySqrt(int x) {
        /*
        if (x == 0) {
            return 0;
        }
        int res = exp(0.5 * log(x))
        return ((long long)(ans + 1) * (ans + 1) <= x ? ans + 1 : ans);
        
        int l = 0, r = x, res = -1;
        while (l <= r) {
            long mid = l + (r - l) / 2;
            if (mid * mid <= x) {
                res = mid;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return res;
    
        */
        if (x == 0) return 0;
        double prev = x;
        while (true) {
            double tmp = 0.5 * (prev + x / prev);
            if (fabs(tmp - prev) < 1e-5) {
                break;
            }
            prev = tmp;
        }
        return int(prev);
    }
};
```



#### [50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        # x ^ n, where n = 2^0 + 2^1 + 2^2 + ... + (?) 2^k
        # x^1 * x^2 * x^4 ... * (?)x^{2^k} 
        if n < 0:
            x = 1.0 / x
            n = -n
        res = 1.0
        tmp = x
        while n > 0:
            if n % 2:
                res *= tmp
            tmp *= tmp
            n = n >> 1
        return res
        
```



#### [48. Rotate Image](https://leetcode.cn/problems/rotate-image/)

rotate the image by **90** degrees (clockwise).

```python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        # 万花筒有 n//2 个
        n = len(matrix)
        def f(i, j):
            return j, n - 1 - i
        for t in range(n // 2):
            for y in range(t, n - t - 1):
                x = t
                prev = matrix[x][y]
                for _ in range(4):
                    nx, ny = f(x, y)
                    tmp = matrix[nx][ny]
                    matrix[nx][ny] = prev
                    prev = tmp
                    x, y = nx, ny
        return matrix

```



#### [47. Permutations II](https://leetcode.cn/problems/permutations-ii/)

all possible permutations with duplicates

```python
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        # 4 3 5/2 [6 5/2 1]
        # next perm: 降序被违反的地方
        # 直到整个排列是降序
        nums.sort()
        res = [nums.copy()]
        while True:
            i = len(nums) - 1
            while i - 1 >= 0 and nums[i - 1] >= nums[i]:
                i -= 1
            if i == 0:
                break # decreasing, finished
            
            curr = nums[i - 1]
            # 比 curr 大的 idx, range [i, len(nums) - 1] 中二分查找
            l = i
            r = len(nums) - 1
            while l < r - 1:
                m = (l + r) // 2
                if nums[m] > curr:
                    l = m
                elif nums[m] <= curr:
                    r = m - 1
            exchangeIdx = r if nums[r] > curr else l
            nums[i - 1] = nums[exchangeIdx]
            nums[exchangeIdx] = curr
            # i 之后 reverse
            j = len(nums) - 1
            while i < j:
                tmp = nums[i]
                nums[i] = nums[j]
                nums[j] = tmp
                i += 1
                j -= 1
            res.append(nums.copy())
        return res

```



#### [46. Permutations](https://leetcode.cn/problems/permutations/)

Return all permutations of an array of distinct integers.

$O(N \times N!)$

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        # [0]
        # [0, 1], [1, 0]
        # ...
        n = len(nums)
        f = [[0]]
        for l in range(2, n + 1):
            # this index is l - 1
            # prev indexes keep same
            g = []
            # this index can be in the place of 0, 1, ..., l - 1
            for sub_order in f:
                for this_place in range(l):
                    g.append(sub_order[: this_place] + [l - 1] + sub_order[this_place:])
            f = g.copy()

        res = []
        for order in f:
            res.append([nums[i] for i in order])

        return res
```