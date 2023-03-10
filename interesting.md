



#### [88. Merge Sorted Array](https://leetcode.cn/problems/merge-sorted-array/) in place

merge to `nums1`. from right to left.

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        # 1 6 7
        # 2 3 4 5 9
        i, j = m - 1, n - 1
        k = m + n - 1
        while k >= 0:
            if i >= 0 and j >= 0:
                if nums1[i] >= nums2[j]:
                    nums1[k] = nums1[i]
                    i -= 1
                else:
                    nums1[k] = nums2[j]
                    j -= 1
                k -= 1
            elif j < 0:
                # 把 i 用完
                nums1[:k + 1] = nums1[:i + 1]
                return
            else:
                nums1[:k + 1] = nums2[:j + 1]
                return
                
```



#### [87. Scramble String](https://leetcode.cn/problems/scramble-string/)

```python
class Solution:
    def isScramble(self, s1: str, s2: str) -> bool:
        @cache
        def dfs(i1, i2, n):
            if s1[i1: i1 + n] == s2[i2: i2 + n]:
                return True
            if Counter(s1[i1: i1 + n]) != Counter(s2[i2: i2 + n]):
                return False
            
            for i in range(1, n):
                # left i, right n - i
                if dfs(i1, i2, i) and dfs(i1 + i, i2 + i, n - i):
                    return True
                # left i, right n - i ; left n - i, right i
                if dfs(i1, i2 + n - i, i) and dfs(i1 + i, i2, n - i):
                    return True
            return False
        res = dfs(0, 0, len(s1))
        dfs.cache_clear()
        return res
```



#### [85. Maximal Rectangle](https://leetcode.cn/problems/maximal-rectangle/)

$O(MN)$

```python
class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        m, n = len(matrix), len(matrix[0])
        res = 0
        h = [0 for _ in range(n)]
        def f(h, l, r):
            tmp = 0
            stack = [l - 1]
            for i in range(l, r + 1):
                while stack[-1] != l - 1 and h[i] <= h[stack[-1]]:
                    popIdx = stack[-1]
                    popIdxLeft = stack[-2]
                    popIdxRight = i
                    tmp = max(tmp, (popIdxRight - popIdxLeft - 1) * h[popIdx])
                    stack.pop(-1)
                stack.append(i)
            while stack[-1] != l - 1:
                popIdx = stack[-1]
                popIdxLeft = stack[-2]
                popIdxRight = r + 1
                # print(popIdxRight,  popIdxLeft)
                tmp = max(tmp, (popIdxRight - popIdxLeft - 1) * h[popIdx])
                stack.pop(-1)
            # print(tmp)
            return tmp

        for i in range(m):
            leftOne = -1
            rightOne = -1
            for j in range(n):
                if matrix[i][j] == '1':
                    if leftOne == - 1:
                        leftOne = j
                        rightOne = j
                    else:
                        rightOne = j
                    h[j] += 1
                    if j == n - 1:
                        res = max(res, f(h, leftOne, rightOne))
                else:
                    h[j] = 0
                    if leftOne != -1:
                        res = max(res, f(h, leftOne, rightOne))
                    leftOne = -1
                    rightOne = -1
        return res
```



#### [84. Largest Rectangle in Histogram](https://leetcode.cn/problems/largest-rectangle-in-histogram/) 单调栈

left nearest smaller, right nearest higher

```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        # -1 (2 1) 5 8 9 6
        # -1  2 (2左边最小的idx是-1)
        # -1  1 (1把2赶走)
        # -1  1    5 8 9 here 6把89赶走, 89右边最近且小的就是6
        # -1  1    5 6 剩下的右边都比自己大
        
        # 相等?
        # (-1) 1(pop) 1 (-1)
        stack = [-1]
        # 进栈
        res = 0
        for i in range(len(heights)):
            while stack[-1] != -1 and heights[i] <= heights[stack[-1]]:
                popIdx = stack[-1]
                popIdxLeft = stack[-2]
                popIdxRight = i
                res = max((popIdxRight - popIdxLeft - 1) * heights[popIdx], res)
                stack.pop(-1)
            stack.append(i)
        while stack[-1] != -1:
            popIdx = stack[-1]
            popIdxLeft = stack[-2]
            popIdxRight = len(heights)
            # print(popIdxLeft, popIdxRight)
            res = max((popIdxRight - popIdxLeft - 1) * heights[popIdx], res)
            stack.pop()
        return res
```



#### [2187. Minimum Time to Complete Trips](https://leetcode.cn/problems/minimum-time-to-complete-trips/)

二分

```python
class Solution:
    def minimumTime(self, time: List[int], totalTrips: int) -> int:
        l = totalTrips // len(time) * min(time)
        r = (totalTrips + len(time) - 1) // len(time) * max(time)
        res = r
        while l <= r:
            mid = (l + r) // 2
            cnt = 0
            for i in range(len(time)):
                cnt += mid // time[i]

            if cnt >= totalTrips:
                res = mid
                r = mid - 1
            else:
                l = mid + 1
        return res
```



#### [2444. Count Subarrays With Fixed Bounds](https://leetcode.cn/problems/count-subarrays-with-fixed-bounds/)

number of subarrays whose min=minK, max=maxK

```python
class Solution:
    def countSubarrays(self, nums: List[int], minK: int, maxK: int) -> int:
        # target 3->5
        # 10 -1 3 4 5 3 -1 5 5 3
        #       a   b a
        #       leftMostAvailable
        #                  b b a
        #                  leftMostAvailable
        res = 0
        minIdx, maxIdx, leftMostAvailable = -1, -1, -1
        for i in range(len(nums)):
            if nums[i] == minK:
                minIdx = i
                if leftMostAvailable == -1:
                    leftMostAvailable = i
            if nums[i] == maxK:
                maxIdx = i
                if leftMostAvailable == -1:
                    leftMostAvailable = i
            
            # above can be updated simutaneously, do not merge
            if nums[i] < minK or nums[i] > maxK:
                leftMostAvailable = -1
                minIdx = -1
                maxIdx = -1
            else:
                if leftMostAvailable == -1:
                    leftMostAvailable = i
                if minIdx != -1 and maxIdx != -1:
                    res += min(minIdx, maxIdx) - leftMostAvailable + 1
            
        return res
```



#### [75. Sort Colors](https://leetcode.cn/problems/sort-colors/)

sort a list of 0, 1, 2 in place

```python
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        l, r = 0, len(nums) - 1

        def swap(i, j):
            tmp = nums[i]
            nums[i] = nums[j]
            nums[j] = tmp

        i = 0
        while i < len(nums):
            if i > r:
                break
            if nums[i] == 0:
                # 换到 leftPtr
                swap(l, i)
                l += 1

            elif nums[i] == 2:
                swap(r, i)
                r -= 1

            if (nums[i] == 0 and i != l - 1) or (nums[i] == 2 and i != r + 1):
                i = i
            else:
                i += 1
```



#### [60. Permutation Sequence](https://leetcode.cn/problems/permutation-sequence/)

$k^{th}$ permutation

```python
class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        # 4*3*2*1=24种
        # 1开头,2开头, 3开头, 4开头. 每组3*2*1=6个
        # 求第8个
        # 8//6=1(2), 第一组, 第二个
        # 第一组 (1, 3, 4)
        # 每个小组 2*1=2个
        # 求第二个
        # 2//2=1 或者说 0余下2
        # 在第0组, 第2个
        # 3开头, 4开头第二个
        # 
        a = list(range(1, n + 1))
        f = [1] * (n + 1) # 阶乘
        for i in range(2, n + 1):
            f[i] = f[i - 1] * i

        res = ""
        for t in range(n, 0, -1):
            sizeGroup = f[t - 1]
            nGroup, offset = k // sizeGroup, k % sizeGroup
            if offset == 0:
                nGroup -= 1
                offset = sizeGroup
            res += str(a[nGroup])
            a.pop(nGroup)
            k = offset
        return res
```



#### [56. Merge Intervals](https://leetcode.cn/problems/merge-intervals/)

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        res = []
        intervals = sorted(intervals, key=lambda x: x[0])
        st, ed = intervals[0]
        for i, j in intervals[1:]:
            if i <= ed:
                ed = max(ed, j)
            else:
                res.append([st, ed])
                st, ed = i, j
        if len(res) == 0 or res[-1][0] != st:
            res.append([st, ed])
        return res
```



#### [55. Jump Game](https://leetcode.cn/problems/jump-game/)

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        maxPos = 0
        for i in range(len(nums)):
            if maxPos < i: return False
            maxPos = max(maxPos, nums[i] + i)
            if maxPos >= len(nums) - 1: return True
        return False
        """
        # a b1 b2 (c1)b3  (c3)c2
        curr = 0
        while curr < len(nums) - 1 and nums[curr] > 0:
            nextPos, maxPos = 0, -1
            if curr + nums[curr] >= len(nums) - 1:
                return True
            for j in range(1, nums[curr] + 1):
                tmp = nums[j + curr] + j + curr
                if tmp > maxPos:
                    maxPos = tmp
                    nextPos = j + curr
            curr = nextPos
        return curr == len(nums) - 1
        """
```



#### [45. Jump Game II](https://leetcode.cn/problems/jump-game-ii/)

`nums[i]` 表示 从下标 `i` 可以跳跃的范围. 从开始跳到结束的最短step. O(n) time.

```python
class Solution {
public:
    int jump(vector<int>& nums) {
        // a b1b2b3 c3c1c2 END
        // 如果bi不能到END 那么还需要ci
        // 所以只在乎能到达bi后面的最远距离
        // 那么跳到哪个bi以后会比较好?
        // (b3, c2] cover了 (b3, c3或者c1)
        int step = 0;
        int i = 0;
        while (i < nums.size() - 1) {
            if (i + nums[i] >= nums.size() - 1) {
                step += 1;
                break;
            }
            int nextPlace = -1;
            int maxDist = -1;
            for (int inc = 1; inc <= nums[i]; ++inc) {
                if (inc + nums[i + inc] > maxDist) {
                    maxDist = inc + nums[i + inc];
                    nextPlace = i + inc;
                }
            }
            i = nextPlace;
            step += 1;
        }
        return step;

        /*
        deque<pair<int, int>> q;
        vector<int> visited = vector<int>(nums.size(), INT_MAX);
        q.push_back(make_pair(0, 0));
        while (!q.empty()) {
            int curr = q.front().first;
            int step = q.front().second;
            q.pop_front();
            if (visited[curr] <= step) continue;
            visited[curr] = step;
            if (curr == nums.size() - 1) {
                break;
            }
            for (int j = 1; j <= nums[curr]; ++j) {
                int nextPlace = curr + j;
                if (nextPlace >= nums.size()) break;
                q.push_back(make_pair(nextPlace, step + 1));
            }
        }
        return visited[nums.size() - 1];
        */
    }
};
```





#### [41. First Missing Positive](https://leetcode.cn/problems/first-missing-positive/)

Return the smallest missing positive integer. O(n) time and uses constant extra space.

```python
class Solution:
    def firstMissingPositive(self, nums: List[int]) -> int:
        # 消失的正数为1,2,3...,n或者n+1
        # 1到n之间, 存不存在可以用i-1位置是否为负数表示
        n = len(nums)
        for i in range(len(nums)): # 所以先把所有人变成+的
            if nums[i] <= 0:
                nums[i] = n + 1
        for i in range(len(nums)):
            if abs(nums[i]) <= n: # 把那个位置变成负数
                nums[abs(nums[i]) - 1] = -abs(nums[abs(nums[i]) - 1])
        for i in range(len(nums)): # 第一个正数在位置i, 说明i+1没遇过
            if nums[i] > 0:
                return i + 1
        return n + 1
```



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

