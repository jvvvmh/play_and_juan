

#### [94. Binary Tree Inorder Traversal](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

递归/stack; morris traversal no extra space 

inorder: left, root, right

```python
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        """
        def f(root):
            if not root:
                return []
            res = []
            if root.left:
                res += f(root.left)
            res.append(root.val)
            if root.right:
                res += f(root.right)
            return res
        return f(root)
        """

        """
        st = []
        res = []
        while (len(st) or root):
            if root:
                st.append(root)
                root = root.left
            else:
                # top->left is null, 先把自己放进答案, 再遍历 right
                tmp = st.pop()
                res.append(tmp.val)
                root = tmp.right
        return res
        """
        # morris traversal
        res = []
        # 把自己和右子树挂在左子树最右节点上
        # 如果没有左子树, root->右
        while root:
            if root.left:
                # 左子树最右节点
                lr = root.left
                while lr.right:
                    lr = lr.right
                newRoot = root.left
                lr.right = root
                root.left = None
                root = newRoot
            else:
                res.append(root.val)
                root = root.right
        return res
```



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





