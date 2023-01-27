#### [101. Symmetric Tree](https://leetcode.cn/problems/symmetric-tree/)

Given root of a binary tree, check whether it's a mirror.

从左 vs 从右边遍历

```C++
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        queue<TreeNode*> q1;
        queue<TreeNode*> q2;
        q1.push(root->left);
        q2.push(root->right);
        while (!q1.empty()) {
            TreeNode* x1 = q1.front();
            TreeNode* x2 = q2.front();
            q1.pop();
            q2.pop();
            if (bool(x1) != bool(x2)) { // null & notNull
                return false;
            }
            if (!x1 && !x2) { // null
                continue;
            }
            if (x1->val != x2->val) { // both not null
                return false;
            }
            q1.push(x1->left); q1.push(x1->right);
            q2.push(x2->right); q2.push(x2->left);
        }
        return true;
    }
};
```





