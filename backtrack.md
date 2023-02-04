

#### [22. Generate Parentheses](https://leetcode.cn/problems/generate-parentheses/)

这不就是第 n 个 Cartalan number嘛. $C(2n,n) - C(2n,n-1) = \dfrac{1}{n+1}C(2n,n)$

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        res = []
        curr = []

        def f(depth, leftCnt):
            if depth == 2 * n + 1:
                res.append("".join(curr))
                return

            rightCnt = depth - 1 - leftCnt

            if leftCnt < n:  
                curr.append("(")
                f(depth + 1, leftCnt + 1)
                curr.pop()

            if rightCnt < leftCnt:
                curr.append(")")
                f(depth + 1, leftCnt)
                curr.pop()
        f(1, 0)
        return res
```



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

