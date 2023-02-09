

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



#### [37. Sudoku Solver](https://leetcode.cn/problems/sudoku-solver/)

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        row = [0] * 9
        col = [0] * 9
        sub = [0] * 9

        target = 0
        for i in range(9):
            for j in range(9):
                if board[i][j] != '.':
                    tmp = int(board[i][j])
                    row[i] |= (1 << tmp)
                    col[j] |= (1 << tmp)
                    idx = 3 * (i // 3) + j // 3
                    sub[idx] |= (1 << tmp)
                    target += 1
        target = 81 - target

        def getNext(i, j):
            if j <= 7:
                return i, j + 1
            return i + 1, 0

        def f(i, j, t):
            if t == 0:
                return True
            ni, nj = getNext(i, j)
            if board[i][j] != '.':
                return f(ni, nj, t)

            # 枚举 1->9
            for x in range(1, 10):
                if row[i] & (1 << x) == 0 and col[j] & (1 << x) == 0 and sub[3 * (i // 3) + j // 3] & (1 << x) == 0:
                    board[i][j] = str(x)
                    row[i] |= 1 << x
                    col[j] |= 1 << x
                    sub[3 * (i // 3) + j // 3] |= 1 << x
                    if f(ni, nj, t - 1):
                        return True
                    else:
                        board[i][j] = '.'
                        row[i] -= (1 << x)
                        col[j] -= (1 << x)
                        sub[3 * (i // 3) + j // 3] -= (1 << x)
            return False
        f(0, 0, target)
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

