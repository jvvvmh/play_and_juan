#### [46. Permutations](https://leetcode.cn/problems/permutations/)

Return all permutations of an array of distinct integers.

$O(N \times N!)$

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        # [0]
        # [0, 1], [1, 0]
        # ...
        n = len(nums)
        f = [[0]]
        for l in range(2, n + 1):
            # this index is l - 1
            # prev indexes keep same
            g = []
            # this index can be in the place of 0, 1, ..., l - 1
            for sub_order in f:
                for this_place in range(l):
                    g.append(sub_order[: this_place] + [l - 1] + sub_order[this_place:])
            f = g.copy()

        res = []
        for order in f:
            res.append([nums[i] for i in order])

        return res
```