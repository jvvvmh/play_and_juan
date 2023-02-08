

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

