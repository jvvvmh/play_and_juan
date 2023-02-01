#### [7. Reverse Integer](https://leetcode.cn/problems/reverse-integer/)

Given a signed 32-bit integer x, return x with its digits reversed. If outside $[-2^{31}, 2^{31} - 1]$, then return 0.

mod 10 取尾数. 如果已经有 214748364(7) 或者 -214748364(8), check 尾数.

```C++
class Solution {
public:
    int reverse(int x) {
        int res = 0;
        while (x) {
            int tmp = x % 10;
            x = x / 10;
            if (res > INT_MAX / 10 || res == INT_MAX / 10 && tmp > 7) {
                return 0;
            }
            if (res < INT_MIN / 10 || res == INT_MIN / 10 && tmp < -8) {
                return 0;
            }
            res = res * 10 + tmp;
        }
        return res;
    }
};
```

#### [8. String to Integer (atoi)](https://leetcode.cn/problems/string-to-integer-atoi/)

deterministic finite automaton, DFA

```python
INT_MAX = 2 ** 31 - 1
INT_MIN = -2 ** 31

class Automaton:
    def __init__(self):
        self.state = 'start'
        self.sign = 1
        self.ans = 0
        self.table = {
            'start': ['start', 'signed', 'in_number', 'end'],
            'signed': ['end', 'end', 'in_number', 'end'],
            'in_number': ['end', 'end', 'in_number', 'end'],
            'end': ['end', 'end', 'end', 'end'],
        }
        
    def get_col(self, c):
        if c.isspace():
            return 0
        if c == '+' or c == '-':
            return 1
        if c.isdigit():
            return 2
        return 3

    def get(self, c):
        self.state = self.table[self.state][self.get_col(c)]
        if self.state == 'in_number':
            self.ans = self.ans * 10 + int(c)
            self.ans = min(self.ans, INT_MAX) if self.sign == 1 else min(self.ans, -INT_MIN)
        elif self.state == 'signed':
            self.sign = 1 if c == '+' else -1

class Solution:
    def myAtoi(self, str: str) -> int:
        automaton = Automaton()
        for c in str:
            automaton.get(c)
            if automaton.state == 'end':
                break
        return automaton.sign * automaton.ans
```

#### [9. Palindrome Number](https://leetcode.cn/problems/palindrome-number/)

判断一个数是不是回文数. 反转右边一半数字: 123 | 4321

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if (x < 0 or (x % 10) == 0 and x != 0):
            return False
        r = 0 # reverted
        while r < x:
            r = r * 10 + x % 10
            x =  x // 10
        return r == x or (r // 10) == x
```

#### [12. Integer to Roman](https://leetcode.cn/problems/integer-to-roman/)

```python
class Solution:

    VALUE_SYMBOLS = [
        (1000, "M"),
        (900, "CM"),
        (500, "D"),
        (400, "CD"),
        (100, "C"),
        (90, "XC"),
        (50, "L"),
        (40, "XL"),
        (10, "X"),
        (9, "IX"),
        (5, "V"),
        (4, "IV"),
        (1, "I"),
    ]

    def intToRoman(self, num: int) -> str:
        res = ""
        for value, symbol in Solution.VALUE_SYMBOLS:
            if num >= value:
                res += (num // value) * symbol
                num = num % value
            if num == 0:
                break
        return res
```

