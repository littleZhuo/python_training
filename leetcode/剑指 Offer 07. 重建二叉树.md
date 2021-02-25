题目比较简单，python实现

```python3
class Solution:
    def buildTree(self, preorder: list, inorder: list) -> TreeNode:
        if len(preorder) == 0:
            return None

        root = TreeNode(preorder[0])

        in_pos = inorder.index(preorder[0])
        left_in = inorder[:in_pos]
        right_in = inorder[in_pos + 1:]

        out_pos = 1 + len(left_in)

        root.left = self.buildTree(preorder[1: out_pos], left_in)
        root.right = self.buildTree(preorder[out_pos: ], right_in)

        return root
```
