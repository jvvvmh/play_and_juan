#### 

#### [28. Find the Index of the First Occurrence in a String](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/) (KMP)

pattern: needle, target: haystack, return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        n = [0] * len(needle) # len of common prefix 除了下标 0 之外
        left = 0 # 在 pattern 中
        for right in range(1, len(needle) - 1):
            while (left > 0 and needle[right] != needle[left]):
                left = n[left - 1]
            if needle[right] == needle[left]:
                left += 1
            n[right]= left
            
        left = 0 # 在 pattern 中
        for right in range(len(haystack)):
            while (left > 0 and haystack[right] != needle[left]):
                left = n[left - 1]
            if haystack[right] == needle[left]:
                left += 1
            if left == len(needle): return right - len(needle) + 1
            
        return -1
```

