# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        # 再帰、bfs、dfs
        # 幅なら余計な探索がいらない
        # dfs
        node = root
        if node is None:
            return 0
        if node.left is None and node.right is None:
            return 1
        if node.left is None:
            return 1 + self.minDepth(node.right)
        if node.right is None:
            return 1 + self.minDepth(node.left)
        return 1 + min(self.minDepth(node.right), self.minDepth(node.left)) 
```

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        # 再帰、bfs、dfs
        # 幅のほうがよさそう
        def min_depth_helper(nodes):
            if not nodes:
                raise ValueError('invalid nodes')
            next_nodes = []
            for node in nodes:
                if node is None:
                    raise ValueError('node is None')
                if node.right is None and node.left is None:
                    return 1
                if node.right is not None:
                    next_nodes.append(node.right)
                if node.left is not None:
                    next_nodes.append(node.left)
            return 1 + min_depth_helper(next_nodes)

        if root is None:
            return 0
        return min_depth_helper([root])
                
                 
```

計算量は前の問題と同じだと思うので割愛

## Step2

### 同じ問題を解いた人のプルリクを見る

- https://github.com/t0hsumi/leetcode/pull/22
- https://github.com/olsen-blue/Arai60/pull/22
- https://github.com/tarinaihitori/leetcode/pull/22/files

感想：

書き方はbfs, dfs, 再帰

どれも読みやすい

条件を書き下さない

https://github.com/olsen-blue/Arai60/pull/22#discussion_r1925337390

計算量について：

bfs はO(min)

dfsはO(n)

Leafの最小値を超えたら探索をやめるようにしたら効率がよさそう。

→最良でO(min), 最悪でO(n)

平均O((min+n) /2)くらいかな

```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        min_depth = sys.maxsize

        def min_depth_helper(root, depth):
            nonlocal min_depth
            if root is None:
                raise ValueError('root is None')
            if depth >= min_depth:
                return 
            if root.right is None and root.left is None:
                min_depth = depth
                return 
            if root.left is not None:
                min_depth_helper(root.left, depth+1)
            if root.right is not None:
                min_depth_helper(root.right, depth+1)
        
        if root is None:
            return 0
        min_depth_helper(root, 1)
        return min_depth       
```

## Step3

### 3回連続で再現できるようになる

https://github.com/Fuminiton/LeetCode/pull/21/files#r1994429862

この考え方がよい

20分　消し忘れていたコードが下のほうにあって答えが合わなかった

4分

2分
```python
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        depth = 0
        nodes = [root]
        while True:
            depth += 1
            next_nodes = []
            for node in nodes:
                if node.right is None and node.left is None:
                    return depth
                if node.right is not None:
                    next_nodes.append(node.right)
                if node.left is not None:
                    next_nodes.append(node.left)
            nodes = next_nodes
```
