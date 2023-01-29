#### [42. Trapping Rain Water](https://leetcode.cn/problems/trapping-rain-water/) 双指针/单调栈/前后DP

Given $n$ non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

```python
class Solution:
    def trap(self, height: List[int]) -> int:
        # 双指针. 只需知道 min(leftMax, rightMax)
        # 每次把不太行的那一根往前挪一挪
        # 100 3 5 20
        # 3 < 5 所以 leftMax <= rightMax
        # 不然5后边的20一定会被100打败, 100不会挪到3
        l = 0
        r = len(height) - 1
        leftMax = height[l]
        rightMax = height[r]

        res = 0
        while l <= r:
            if height[l] <= height[r]:
                leftMax = max(leftMax, height[l])
                res += leftMax - height[l]
                l += 1
            else:
                rightMax = max(rightMax, height[r])
                res += rightMax - height[r]
                r -= 1
        return res

        """
        # 单调栈stack中存了index, index所对应的height单调递减.
        # 8 6 (2) [5]
        #     (2) 被move了, 相当于他填好水后已经达到了[5]的高度.
        # 8 6 (5) 5 
        res = 0
        s = []
        for i, h in enumerate(height):
            while len(s) and h > height[s[-1]]:
                q = s.pop()
                if len(s) == 0: break
                leftIdx = s[-1]
                rightIdx = i
                maxH = min(height[leftIdx], height[rightIdx])
                length = rightIdx - leftIdx - 1
                thisH = height[q]
                res += length * (maxH - thisH)
            s.append(i)
        return res
        """

        """
        # O(n) 给出leftMax数组,
        # O(n) 遍历
        # max( min(leftMax, rightMax) - height[i], 0 )
        leftMax = [-1]
        for i in height:
            leftMax.append( max(leftMax[-1], i) )
        leftMax = leftMax[1:]
        rightMax = [-1]
        for i in height[::-1]:
            rightMax.append( max(rightMax[-1], i) )
        rightMax = rightMax[1:]
        res = 0
        n = len(height)
        for i in range(n):
            res += max( min(leftMax[i], rightMax[n - 1 - i]) - height[i], 0 )
        return res
        """
```



#### [189. Rotate Array](https://leetcode.cn/problems/rotate-array/)

Rotate the array to the right by k steps. $O(n)$

(1) greatest common divisor 个环 (2) 翻转整个数组 + 翻转左k个 + 翻转右边数组 

```python
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        n = len(nums)
        k = k % n
        s = 0
        for st_idx in range(n):
            i = st_idx
            carry = nums[i]
            while True:
                if (i + k) % n == st_idx:
                    nums[st_idx] = carry
                    s += 1
                    break
                tmp = nums[(i + k) % n]
                nums[(i + k) % n] = carry
                carry = tmp
                i = (i + k) % n
                s += 1
            # 回到了 st_idx
            if s == n:
                return
        """
        n = len(nums)
        k = k % n
        # 翻转整个数组
        def swap(nums, st, ed):
            for i in range(st, (st + ed) // 2):
                tmp = nums[i]
                nums[i] = nums[(st + ed - 1) - i]
                nums[(st + ed - 1) - i ] = tmp
        swap(nums, 0, len(nums))
        swap(nums, 0, k)
        swap(nums, k, len(nums))
```

