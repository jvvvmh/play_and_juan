#### [17. Letter Combinations of a Phone Number](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```
Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        mp = {
            "2": "abc",
            "3": "def",
            "4": "ghi",
            "5": "jkl",
            "6": "mno",
            "7": "pqrs",
            "8": "tuv",
            "9": "wxyz",
        }
        n = len(digits)
        if n == 0:
            return []
        res = []
        tmp = []
        def f(idx):
            if idx == n:
                res.append("".join(tmp))
                return
            for c in mp[digits[idx]]:
                tmp.append(c)
                f(idx + 1)
                tmp.pop()
        f(0)
        return res
```

