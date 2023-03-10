#### [18. 4Sum](https://leetcode.cn/problems/4sum/) 排序 + 两重循环 + 双指针

```python
class Solution:
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        nums.sort()
        res = []
        n = len(nums)
        print(nums)
        inf = 10**9 + 10
        prev_i = inf
        prev_j = inf
        prev_k = inf
        prev_l = inf
        for i in range(n - 3):
            if nums[i] == prev_i: continue
            if nums[i] * 4 > target: breask
            prev_i = nums[i]
            for j in range(i + 1, n - 2):
                if nums[j] == prev_j: continue
                if nums[i] + nums[j] * 3 > target: break
                prev_j = nums[j]
                k = j + 1
                l = n - 1
                while k < l:
                    s = nums[i] + nums[j] + nums[k] + nums[l] - target
                    if s == 0:
                        if nums[k] != prev_k:
                            prev_k = nums[k]
                            prev_l = nums[l]
                            res.append([nums[i], nums[j], nums[k], nums[l]])
                        k += 1
                        l -= 1
                    elif s > 0:
                        l -= 1
                    else:
                        k += 1
                prev_k, prev_l = inf, inf
            prev_j = inf
        return res
```



#### [15. 3Sum ](https://leetcode.cn/problems/3sum/) 排序 + 双指针

Must not contain duplicate triplets. $O(N^2)$

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        res = []
        nums = sorted(nums)
        n = len(nums)
        last_i, last_j = 10**6, 10**6
        for i in range(n):
            if nums[i] == last_i: continue
            last_i = nums[i]
            for j in range(i + 1, n - 1):
                if nums[j] == last_j: continue
                last_j = nums[j]
                s = nums[i] + nums[j]
                k = n - 1
                while (k >= j + 2 and s + nums[k] > 0):
                    k -= 1
                if s + nums[k] == 0:
                    res.append([nums[i], nums[j], nums[k]])
        return res
```



#### [3. Longest Substring Without Repeating Characters](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        res = 0
        l = 0
        v = set() # visited
        for r in range(len(s)):
            if s[r] in v:
                while s[l] != s[r]:
                    v.remove(s[l])
                    l += 1
                l = min(l + 1, r)
            else:
                v.add(s[r])
                res = max(res, r - l + 1)
        return res
```



#### [30. Substring with Concatenation of All Words](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)

All words same length. Find all substrings that contains a permutation of words.

```python
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        slide = len(words[0])
        res = []
        # 每个答案的起始坐标按照 mod slide 来分成了 slide 类
        for t in range(slide):
            # 从下标 t 开始, 看长度为 slide 的串
            target = Counter(words)
            sTarget = sum(target.values())
            l = t
            for r in range(l, len(s) - slide + 1, slide):
                # r + slide - 1 <= len(s) - 1
                # r <= len(s) - slide
                newStr = s[r: r + slide]
                if newStr in target:
                    if target[newStr] > 0:
                        target[newStr] -= 1
                        sTarget -= 1
                        if sTarget == 0:
                            res.append(l)
                    else: # newStr 太多了, 移动左指针
                        while s[l: l + slide] != newStr:
                            target[s[l: l + slide]] += 1
                            sTarget += 1
                            l += slide
                        l = min(l + slide, r) # 可能重合
                        if ((r - l) / slide + 1 == len(words)):
                            res.append(l)
                else:
                    # s[r: r + slide] newStr is a wrong str
                    # 删了之前的, 一起到 r + slide
                    while l != r:
                        target[s[l: l + slide]] += 1
                        sTarget += 1
                        l += slide
                    l += slide

        return res
```



#### [76. Minimum Window Substring](https://leetcode.cn/problems/minimum-window-substring/)

Two strings s and t, return the minimum window substring of s such that every character in t (including duplicates) is included in the window.

left, right 一开始都在 target char上. r 指针不停往右动 (记录除了第一次的好位置), 直到满足条件, 此时优化 left 指针 (用记录的位置跳).

```python
class Solution:
    def check(self, target):
        for v in target.values():
            if v > 0:
                return False
        return True

    def movable(self, c, target):
        return target[c] + 1 <= 0

    def minWindow(self, s: str, t: str) -> str:
        target = Counter(t)
        inf = 10 ** 5 + 10
        res = (0, inf)
        st = 0
        while s[st] not in target:
            st += 1
            if st >= len(s):
                break
        l = st
        prevIdx = deque([])
        for r in range(st, len(s)):
            newChar = s[r]
            if newChar in target:
                target[newChar] -= 1
                if r != st:
                    prevIdx.append(r)
                if self.check(target):
                    # move left
                    while l < r and self.movable(s[l], target):
                        target[s[l]] += 1
                        l = prevIdx.popleft()
                    if r - l < res[1] - res[0]:
                        res = (l, r)
   
        if res[1] - res[0] == inf:
            return ""
        return s[res[0]: res[1] + 1]
```



#### [159. Longest Substring with At Most Two Distinct Characters](https://leetcode.cn/problems/longest-substring-with-at-most-two-distinct-characters/)

```python
class Solution:
    def lengthOfLongestSubstringTwoDistinct(self, s: str) -> int:
        v = {}
        l = 0
        res = 0
        for r in range(len(s)):
            newChar = s[r]
            if newChar in v:
                v[newChar] += 1
            else:
                if len(v) == 0:
                    v[newChar] = 1
                elif len(v) == 1:
                    v[newChar] = 1
                else:
                    # 已经有了两个, 要pop掉第一个
                    while len(v) == 2:
                        v[s[l]] -= 1
                        if v[s[l]] == 0:
                            v.pop(s[l])
                        l += 1
                    v[s[r]] = 1
            res = max(res, r - l + 1)
        return res
```

#### [189. Rotate Array](https://leetcode.cn/problems/rotate-array/)

Rotate the array to the right by k steps. $O(n)$

greatest common divisor

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

#### [209. Minimum Size Subarray Sum](https://leetcode.cn/problems/minimum-size-subarray-sum/)

Given an array of positive integers nums and a positive integer target, return the **minimal length of a subarray** whose sum >= target.

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        # 移动 r 直到 >= target
        # 移动 l 直到快要 <  target
        l = 0
        s = 0
        res = 10 ** 5 + 10
        for r in range(len(nums)):
            s += nums[r]
            if s >= target:
                if nums[r] >= target:
                    return 1
                else:
                    while s - nums[l] >= target: # 恰好不能再减nums[l]啦
                        s -= nums[l]
                        l += 1
                    res = min(res, r - l + 1)
        return 0 if res == 10 ** 5 + 10 else res
```

#### [219. Contains Duplicate II](https://leetcode.cn/problems/contains-duplicate-ii/)

Exist two distinct indices i and j such that nums[i] == nums[j] and abs(i - j) <= k?

```python
class Solution:
    def containsNearbyDuplicate(self, nums: List[int], k: int) -> bool:
        n = len(nums)
        v = set()
        k = min(k, n - 1) 
        for i in range(n):
            if i > k:
                v.remove(nums[i - k - 1])
            if nums[i] in v:
                return True
            v.add(nums[i])
        return False
```

