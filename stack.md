

#### [71. Simplify Path](https://leetcode.cn/problems/simplify-path/)

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        # null (multi slash)
        # . (skip curr path)
        # .. (pop stack if not empty)
        # abc123_ (push back)
        flow = path.split('/')
        stack = []
        for x in flow:
            print(x)
            if x == '..':
                if stack:
                    stack.pop()
            elif x not in ['.', '']: # abc123_
                stack.append(x)
        return '/' + '/'.join(stack)
```



#### [20. Valid Parentheses](https://leetcode.cn/problems/valid-parentheses/)

```python
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) % 2: return False
        d = {'(': ')', '{': '}', '[': ']'}
        stack = []
        for ch in s:
            if ch in d: # 左括号
                stack.append(ch)
            else: # 右括号
                if len(stack) == 0:
                    return False
                if d[stack[-1]] != ch:
                    return False
                stack.pop()
        return len(stack) == 0
```

