[TOC]



#### [32. Longest Valid Parentheses](https://leetcode.cn/problems/longest-valid-parentheses/)

- bi-directional $O(n)$ time, $O(1)$ space
- dp[i] 以 i 结尾的最大长度 $O(n)$ space
- stack. bottom is the rightmost unmatched ')' index, initialize with -1

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        # () ((()   ()))   )
        res = 0
        l, r = 0, 0
        curr_l, curr_r = 0, 0
        while r <= len(s) - 1:
            if s[r] == '(':
                curr_l += 1
                r += 1
            else:
                # 右括号
                if curr_r == curr_l:
                    curr_l, curr_r = 0, 0
                    l, r = r + 1, r + 1
                else:
                    curr_r += 1
                    if curr_l == curr_r:
                        res = max(res, curr_l * 2)
                    r += 1
        if curr_l == curr_r: return res
    
        l, r = len(s) - 1, len(s) - 1
        curr_l, curr_r = 0, 0
        while l >= 0:
            if s[l] == ')':
                curr_r += 1
                l -= 1
            else:
                if curr_r == curr_l:
                    curr_l, curr_r = 0, 0
                    l, r = l - 1, l - 1
                else:
                    curr_l += 1
                    if curr_l == curr_r:
                        res = max(res, curr_l * 2)
                    l -= 1
        return res
```



#### [31. Next Permutation](https://leetcode.cn/problems/next-permutation/)

2 (1) [5] 4 3 0

1. 找到下降的 head [5] (如果是 nums[0] 则之间翻转)
2. 把 1 和后面最小的但比 1 大的数字 3 交换, 得到 2 3 [5] 3 1 0
3. [5] 3 1 0 翻转

```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        if len(nums) == 0: return
        i = len(nums) - 1
        while (i >= 1 and nums[i - 1] >= nums[i]):
            i -= 1
        if i == 0:
            l = 0
            r = len(nums) - 1
            while l < r:
                nums[l], nums[r] = nums[r], nums[l]
                l += 1
                r -= 1
        else:
            j = i
            while nums[j] > nums[i - 1]:
                if j == len(nums) - 1 or nums[j + 1] <= nums[i - 1]:
                    break
                j += 1
            nums[i - 1], nums[j] = nums[j], nums[i - 1]
            # from i to len(nums) - 1, reverse
            l = i
            r = len(nums) - 1
            while l < r:
                nums[l], nums[r] = nums[r], nums[l]
                l += 1
                r -= 1

```



#### [19. Remove Nth Node From End of List](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/) 快慢指针

创建一个dummy指针指向head. 快指针先走n步, 一起走到链表末尾, 慢指针就是to remove的前驱节点.

```python
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        # () 1 2 3 4 5 6 7
        #            *   *
        dummy = ListNode(0, head)
        l, r = dummy, dummy
        for _ in range(n):
            r = r.next
        while r.next is not None:
            l = l.next
            r = r.next
        l.next = l.next.next
        return dummy.next
```



#### [16. 3Sum Closest](https://leetcode.cn/problems/3sum-closest/)

Return *the sum of the three integers*.

```python
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        diff = 10**5
        res = 0
        n = len(nums)
        nums.sort()
        for i in range(n - 2):
            j = i + 1
            k = n - 1
            while j < k:
                s = nums[i] + nums[j] + nums[k]
                if abs(s - target) < diff:
                    diff = abs(s - target)
                    res = s
        
                if s == target:
                    return target
                elif s > target:
                    k -= 1
                else:
                    j += 1

        return res
```



#### [11. Container With Most Water](https://leetcode.cn/problems/container-with-most-water/)

Find two lines such that the container |__| contains the most water.

两边到中间收缩, 每次移动短板 (drop out cases which are not better than the current)

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        l = 0
        r = len(height) - 1
        res = 0
        while l <= r - 1:
            res = max(res, min(height[l], height[r]) * (r - l))
            # 移动短板
            if height[l] <= height[r]:
                l += 1
            else:
                r -= 1
        return res

```

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

