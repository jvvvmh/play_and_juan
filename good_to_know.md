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

