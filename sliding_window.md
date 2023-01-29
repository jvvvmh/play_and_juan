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

