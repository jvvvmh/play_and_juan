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

