

#### [79. Word Search](https://leetcode.cn/problems/word-search/)

whether word exists in board.

```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        m, n = len(board), len(board[0])
        v = [[False] * n for _ in range(m)]
        def f(i, j, depth):
            if depth == len(word) - 1:
                return True
            v[i][j] = True
            for dx, dy in [(0, 1), (0, -1), (-1, 0), (1, 0)]:
                x, y = i + dx, j + dy
                if 0 <= x and x < m and 0 <= y < n:
                    if (not v[x][y]) and word[depth + 1] == board[x][y]:
                        if f(x, y, depth + 1):
                            return True
            v[i][j] = False
            return False
        for i in range(m):
            for j in range(n):
                if board[i][j] == word[0]:
                    if f(i, j, 0):
                        return True
        return False
```



#### [78. Subsets](https://leetcode.cn/problems/subsets/)

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def f(i):
            if i == len(nums):
                return [[]]
            tmp = f(i + 1)
            n = len(tmp)
            for x in range(n):
                tmp.append(tmp[x] + [nums[i]])
            return tmp
        return f(0)
```



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



#### [40. Combination Sum II](https://leetcode.cn/problems/combination-sum-ii/)

```python
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        tmp = []
        freq = Counter(candidates)
        candidates = list(freq.keys())
        
        def f(idx, curr):
            nonlocal tmp
            nonlocal res
            if curr == target:
                res.append(tmp.copy())
                return
            if idx == len(candidates):
                return

            f(idx + 1, curr)
            cnt = 0
            for _ in range(1, freq[candidates[idx]] + 1):
                if curr + candidates[idx] > target:
                    break
                cnt += 1
                curr += candidates[idx]
                tmp.append(candidates[idx])
                f(idx + 1, curr)
            if cnt != 0:
                tmp = tmp[:-cnt]

        f(0, 0)
        return res
```



#### [39. Combination Sum](https://leetcode.cn/problems/combination-sum/)

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        candidates.sort()
        res = []
        tmp = []
        def f(idx, curr, res):
            if curr == target:
                res.append(tmp.copy())
                return res
            if idx >= len(candidates):
                return res
            for i in range(idx, len(candidates)):
                if curr + candidates[i] > target:
                    break
                curr += candidates[i]
                tmp.append(candidates[i])
                res = f(i, curr, res)
                tmp.pop()
                curr -= candidates[i]
            return res
        return f(0, 0, res)
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



#### [51. N-Queens](https://leetcode.cn/problems/n-queens/)

```python
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        def generateBoard():
            board = []
            for i in range(n):
                row = ["."] * n
                row[queens[i]] = "Q"
                board.append("".join(row))
            return board

        def backtrack(row: int):
            if row == n:
                board = generateBoard()
                solutions.append(board)
            else:
                for i in range(n):
                    if i in columns or row - i in diagonal1 or row + i in diagonal2:
                        continue
                    queens[row] = i
                    columns.add(i)
                    diagonal1.add(row - i)
                    diagonal2.add(row + i)
                    backtrack(row + 1)
                    columns.remove(i)
                    diagonal1.remove(row - i)
                    diagonal2.remove(row + i)
                    
        solutions = []
        queens = [-1] * n # 每一行放在第几列
        columns = set()
        diagonal1 = set()
        diagonal2 = set()
        backtrack(0)
        return solutions
```

