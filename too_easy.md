

#### [92. Reverse Linked List II](https://leetcode.cn/problems/reverse-linked-list-ii/)

reverse the position from left to right

```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        p = ListNode(val=0, next=head)
        
        prev, tmp = p, head
        for i in range(left - 1):
            prev = tmp
            tmp = tmp.next
        prevL = prev
        l = tmp
        

        fwd = tmp.next
        for j in range(right - left):
            # prev tmp fwd
            prev = tmp
            tmp = fwd
            fwd = fwd.next
            tmp.next = prev
        
        r = tmp
        prevL.next = r
        l.next = fwd
 
        return p.next
```





#### [1539. Kth Missing Positive Number](https://leetcode.cn/problems/kth-missing-positive-number/)

```
Input: arr = [1,2,3,4], k = 2
Output: 6
```

```python
class Solution:
    def findKthPositive(self, arr: List[int], k: int) -> int:
        cnt = 0
        prev = 0
        for n in arr:
            if (n - prev) - 1 + cnt >= k:
                print("here")
                return (k - cnt) + prev
            else:
                cnt += (n - prev) - 1
            prev = n
        return prev + k - cnt
```



#### [80. Remove Duplicates from Sorted Array II](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/)

at most store 2

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        prev = None
        cnt = 0
        k = 0
        for ch in nums:
            if ch != prev:
                prev = ch
                cnt = 1
                nums[k] = ch
                k += 1
            else:
                if cnt == 2:
                    continue
                # cnt == 1
                cnt = 2
                nums[k] = ch
                k += 1
        return k

```



#### [73. Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)

```C++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        // x[i][0] = 0 => row zero
        // x[0][i] = 0 => col zero
        bool col0 = false;
        for (int i = 0; i < matrix.size(); ++i) {
            for (int j = 0; j < matrix[0].size(); ++j) {
                if ((matrix[i][j] == 0) && (j != 0)) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                    cout<<"         i"<<i<<endl;
                    cout<<"           j"<<j<<endl;
                } else if (matrix[i][j] == 0 && j == 0) {
                    col0 = true;
                }
            }
        }

        for (int i = 1; i < matrix.size(); ++i) {
            if (matrix[i][0] == 0) {
                for (int j = 1; j < matrix[0].size(); ++j) {
                    matrix[i][j] = 0;
                }
            }
        }

        for (int j = 0; j < matrix[0].size(); ++j) {
            if (j && (matrix[0][j] == 0)) {
                for (int i = 1; i < matrix.size(); ++i) {
                    matrix[i][j] = 0;
                }
            }
        }

        // first row
        if (matrix[0][0] == 0) {
            for (int j = 1; j < matrix[0].size(); ++j) {
                matrix[0][j] = 0;
            }
        }

        // first col
        if (col0) {
            for (int i = 0; i< matrix.size(); ++i) {
                matrix[i][0] = 0;
            }
        }
    }
};
```



#### [61. Rotate List](https://leetcode.cn/problems/rotate-list/)

rotate the list by `k` places.

```python
class Solution:
    def rotateRight(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if head is None: return head
    
        # 环
        tail = head
        n = 1
        while tail.next:
            tail = tail.next
            n += 1
        k = k % n
        tail.next = head
    
        # 从head开始移动n-k-1步, 要断掉他和下一个 返回下一个
        for _ in range(n - k - 1):
            head = head.next
        res = head.next
        head.next = None
        return res
```



#### [59. Spiral Matrix II](https://leetcode.cn/problems/spiral-matrix-ii/)

顺时针填数字

```python
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        # a a a a
        # a b b a
        # a b b a
        # a a a a
        cnt = 1
        res = [[0] * n for _ in range(n)]
        for t in range(n // 2):
            # 左上角 (t, t) 循环四次
            for j in range(t, n - t - 1):
                res[t][j] = cnt
                cnt += 1
            for i in range(t, n - t - 1):
                res[i][n - t - 1] = cnt
                cnt += 1
            for j in range(n - t - 1, t, -1):
                res[n - t - 1][j] = cnt
                cnt += 1
            for i in range(n - t - 1, t, -1):
                res[i][t] = cnt
                cnt += 1
        if n % 2:
            res[n // 2][n // 2] = cnt
        return res

```



#### [57. Insert Interval](https://leetcode.cn/problems/insert-interval/)

given an array of non-overlapping intervals sorted in ascending order by $start_i$. insert newInterval.

```python
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        if len(intervals) == 0:
            return [newInterval]
        # left fast in, right slow out
        i = 0
        while intervals[i][1] < newInterval[0]:
            i += 1
            if i == len(intervals):
                intervals.append(newInterval)
                return intervals
    
        j = len(intervals) - 1
        while intervals[j][0] > newInterval[1]:
            j -= 1
            if j == -1:
                intervals = [newInterval] + intervals
                return intervals
 
        # 最后一次交错是和i和j
        st = min(intervals[i][0], newInterval[0])
        ed = max(intervals[j][1], newInterval[1])
        res = []
        res += intervals[:i]
        res.append([st, ed])
        res += intervals[j + 1:]
        return res
```



#### [54. Spiral Matrix](https://leetcode.cn/problems/spiral-matrix/)

```python
class Solution:
    def spiralOrder(self, matrix: List[List[int]]) -> List[int]:
        # 12
        # 34

        #  a 
        # d b
        #  c   
        res = []
        a = 0
        b = len(matrix[0]) - 1
        c = len(matrix) - 1
        d = 0
        while True:
            for i in range(d, b + 1):
                res.append(matrix[a][i])
            a += 1
            if a > c: break
            for i in range(a, c + 1):
                res.append(matrix[i][b])
            b -= 1
            if b < d: break
            for i in range(b, d - 1, -1):
                res.append(matrix[c][i])
            c -= 1
            if a > c: break
            for i in range(c, a - 1, -1):
                res.append(matrix[i][d])
            d += 1
            if b < d: break
        return res

```



#### [49. Group Anagrams](https://leetcode.cn/problems/group-anagrams/)

`Output: [["bat"],["nat","tan"],["ate","eat","tea"]]`

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        mp = collections.defaultdict(list)
        for s in strs:
            cnt = [0] * 26 # 第 i 位表示那个字母出现了几次
            for ch in s:
                cnt[ord(ch) - ord("a")] += 1
            mp[tuple(cnt)].append(s)
        return list(mp.values())

```



#### [35. Search Insert Position](https://leetcode.cn/problems/search-insert-position/)

the index where the target can be inserted into the sorted arr of distinct integers

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        l, r = 0, len(nums) - 1
        # l <= x <= r
        while l <= r - 1:
            mid = (l + r) // 2
            if nums[mid] > target:
                r = mid - 1
            elif nums[mid] < target:
                l = mid + 1
            else:
                return mid
        # 此时 l == r
        if nums[l] >= target:
            return l
        return l + 1
```



#### [34. Find First and Last Position of Element in Sorted Array](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        n = len(nums)
        l, r = 0, n - 1
        find = -1
        while l <= r:
            m = (l + r) // 2
            if nums[m] > target:
                r = m - 1
            elif nums[m] == target:
                find = m
                break
            else:
                l = m + 1
        if find == -1:
            return [-1, -1]
        
        # 找到 find 左边 第一个比 target 小的位置
        # 找到 find 右边 第一个比 target 大的位置
        leftSt = -1
        l, r = 0, find
        if find == 0:
            leftSt = 0
        else:
            while l <= r - 2:
                m = (l + r) // 2
                if nums[m] == target:
                    r = m
                else:
                    l = m
            leftSt = r if nums[l] != target else l

        rightEd = -1
        l, r = find, n - 1
        if find == n - 1:
            rightEd = n - 1
        else:
            while l <= r - 2:
                m = (l + r) // 2
                if nums[m] == target:
                    l = m
                else:
                    r = m
            rightEd = l if nums[r] != target else r

        return [leftSt, rightEd]
```



#### [33. Search in Rotated Sorted Array](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

find the place of target in `[4,5,6,7,0,1,2]`  (rotate at index 5)

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        l = 0
        r = len(nums) - 1
        offset = 0

        while l < r:
            m = (l + r) // 2
            if nums[m] < nums[0]:
                r = m
            elif nums[m] > nums[0]:
                l = m + 1
            else:
                offset = 0 if nums[0] < nums[1] else 1
                break
        
        if l == r:
            if nums[l] > nums[0]:
                offset = 0
            else:
                offset = l

        def getNewIdx(i):
            return (i + offset) % len(nums)
        
        l = 0
        r = len(nums) - 1
        find = -1
        while l <= r:
            m = (l + r) // 2
            newIdx = getNewIdx(m)
            if nums[newIdx] == target:
                find = newIdx
                break
            elif nums[newIdx] > target:
                r = m - 1
            else:
                l = m + 1

        return find
```



#### [25. Reverse Nodes in k-Group](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if k == 1:
            return head
        dummy = ListNode(next=head)
        p1 = dummy
        while True:
            p2 = p1
            if not p1: break
            for _ in range(k):
                if p2.next:
                    p2 = p2.next
                else: 
                    return dummy.next

            nextSt = p2.next
            thisSt = p1.next
            
            prev = p1.next
            tmp = p1.next.next
            # thisSt <- ... < thisEd
            for _ in range(k - 1):
                nextTmp = tmp.next
                tmp.next = prev
                prev = tmp
                tmp = nextTmp

            p1.next = p2
            thisSt.next = nextSt

            p1 = thisSt

        return dummy.next
```



#### [24. Swap Nodes in Pairs](https://leetcode.cn/problems/swap-nodes-in-pairs/)

Given a linked list, swap every two adjacent nodes and return its head.

```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(next=head)
        tmp = dummy
        while tmp.next and tmp.next.next:
            thisPairSt = tmp.next
            thisPairEd = thisPairSt.next

            thisPairSt.next = thisPairEd.next
            thisPairEd.next = thisPairSt
            tmp.next = thisPairEd

            tmp = thisPairSt

        return dummy.next
```



#### [23. Merge k Sorted Lists](https://leetcode.cn/problems/merge-k-sorted-lists/)

时间 $O(kn \log(k))$  空间 $O(\log(k))$

```c++
class Solution {
public:
    struct cmp {
        bool operator ()(const ListNode* a, const ListNode* b) {
            return a->val > b->val;
        }
    };
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<ListNode*, vector<ListNode*>, cmp> q;
        // 定义 greater 使得最小的在前面
        for (auto p : lists) {
            if (p) {
                q.push(p);
            }
        }
        ListNode * ans = new ListNode();
        ListNode * tmp = ans;
        while (q.size()) {
            auto top = q.top();
            q.pop();
            tmp -> next = top;
            if (top->next) {
                q.push(top->next);
            }
            tmp = tmp->next;
        }
        return ans->next;
    }
};
```



#### [21. Merge Two Sorted Lists](https://leetcode.cn/problems/merge-two-sorted-lists/)

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode()
        p1 = list1
        p2 = list2
        tmp = dummy
        while p1 and p2:
            if p1.val <= p2.val:
                tmp.next = p1
                p1 = p1.next
                tmp = tmp.next
            else:
                tmp.next = p2
                p2 = p2.next
                tmp = tmp.next
        if p1:
            tmp.next = p1
        elif p2:
            tmp.next = p2
        return dummy.next
```



#### [26. Remove Duplicates from Sorted Array](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        idx = -1
        prev = 999
        for n in nums:
            if n != prev:
                idx += 1
                nums[idx] = n
                prev = n
        return idx + 1
```



#### [36. Valid Sudoku](https://leetcode.cn/problems/valid-sudoku/)

```python
class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        n = len(board)
        row = [0] * n
        col = [0] * n
        sub = [0] * n
        for i in range(n):
            for j in range(n):
                tmp = board[i][j]
                if tmp != '.':
                    tmp = int(tmp)
                    if row[i] & (1 << tmp):
                        return False
                    else:
                        row[i] |= (1 << tmp)
                    if col[j] & (1 << tmp):
                        return False
                    else:
                        col[j] |= (1 << tmp)
                    idx = i // 3 * 3 + j // 3
                    if sub[idx] & (1 << tmp):
                        return False
                    else:
                        sub[idx] |= (1 << tmp)
        return True
```

