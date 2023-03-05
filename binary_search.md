

#### [81. Search in Rotated Sorted Array II](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/)

target num exists in the shifted sorted array (have duplicates)? worst $O(N)$ if all equal but no target.

```python
class Solution:
    def search(self, nums: List[int], target: int) -> bool:

        # 2 2 3 3 4 | 1 2 2

        # 左边有多少个nums[0]
        cnt1 = 0
        for x in nums:
            if x == nums[0]:
                cnt1 += 1
            else:
                break

        # 右边有多少个nums[0]
        cnt2 = 0
        for i in range(len(nums) - 1, cnt1 - 1, -1):
            if nums[i] == nums[0]:
                cnt2 += 1
            else:
                break

        if cnt1 + cnt2 == len(nums):
            return target == nums[0]

        l = cnt1
        r = len(nums) - cnt2 - 1
        # from i to j, which one starts to < nums[0]?
        offset = -1
        while l <= r:
            if l >= r - 1:
                if nums[l] < nums[0]:
                    offset = l
                elif nums[r] < nums[0]:
                    offset = r
                break
            if nums[l] < nums[0]:
                offset = l
            mid = (l + r) // 2
            if nums[mid] > nums[0]:
                l = mid + 1
            else:
                r = mid

        if offset == -1: # 全都比nums[0]大
            offset = len(nums) - cnt2

        # search for target in original arr
        l = 0
        r = len(nums) - 1

        def getOffsetIdx(i):
            '''unshifted array idx => current idx'''
            return (i + offset) % len(nums)

        while l <= r:
            mid = (l + r) // 2
            if nums[getOffsetIdx(mid)] == target:
                return True
            elif nums[getOffsetIdx(mid)] > target:
                r = mid - 1
            else:
                l = mid + 1
        return False
```



#### [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)

每一行递增, 每一行比上一行增. 查找 target 是否存在.

```python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        # search in first col
        # then search in that row
        first_col = [matrix[i][0] for i in range(len(matrix))]
        l, r = 0, len(first_col) - 1
        # * <= x < ...
        rowIdx = -1
        while True:
            if target < first_col[l]:
                return False
            elif target >= first_col[r]:
                rowIdx = r
                break
            else:
                # 存在 *, such that * <= target < ...
                if l == r - 1:
                    rowIdx = l
                    break
                mid = (l + r) // 2
                if first_col[mid] < target:
                    l = mid
                elif first_col[mid] == target:
                    return True
                else:
                    r = mid

        row = matrix[rowIdx]
        l, r = 0, len(row) - 1
        while l <= r:
            if r == l:
                return row[l] == target
            else:
                mid = (l + r) // 2
                if row[mid] == target:
                    return True
                elif row[mid] > target:
                    r = mid - 1
                else:
                    l = mid + 1
        return False
```



#### [4. Median of Two Sorted Arrays](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        # 求第k小的数字
        # 左边有 k-1 个, 右边有(m + n - k)个

        # nums1[i]
        # 左边已经有 i 个             还需要 k-1-i 个
        # 右边已经有 m - (i + 1)个    还需要 (m+n-k) - (m-i-1) = n-k+i+1个
        inf = 10**6 + 10
        def search(x, y, k):
            m = len(x)
            n = len(y)
            l = 0
            r = m - 1
            while l <= r:
                i = (l + r) // 2
                n_left = k - 1 - i
                if n_left == 0:
                    pass # 左边ok
                else:
                    if n_left < 0:
                        # too big
                        r = i - 1
                        continue
                    else: # n_left > 0, check y[n_left - 1]
                        if n_left - 1 >= len(y):
                            # too small
                            l = i + 1
                            continue
                        else:
                            # n_left - 1 is a good index
                            if y[n_left - 1] > x[i]:
                                # too small
                                l = i + 1
                                continue
                # check y[n_left] 是否 >= x[i]
                if n_left < n and y[n_left] < x[i]:
                    # too big
                    r = i - 1
                    continue
                else:
                    return x[i]
            return search(y, x, k)

        if (len(nums1) + len(nums2)) % 2:
            return search(nums1, nums2, (len(nums1) + len(nums2) + 1) // 2)
        else:
            v1 = search(nums1, nums2, (len(nums1) + len(nums2)) // 2 + 1)
            v2 = search(nums1, nums2, (len(nums1) + len(nums2)) // 2)
            return (v1 + v2) / 2.0
```

