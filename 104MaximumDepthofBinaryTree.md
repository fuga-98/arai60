# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

結構ミスした、あまり自然でない書き方をしたからだと思う。手作業で考えてからやろう。

時間計算量はO(n) 空間計算量はO(logn) nは1000

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # rootの長さで計算できそう
        # （rootの長さ、深さ）として
        # (1,1)(2,2)(3,2),(4,3)(7,3)(8,4)
        # listが来るのかと思ってた
        if root is None:
            return 0
        depth = 0
        same_depth = [root]
        next_depth = []

            
        while True:
            if not same_depth:
                if not next_depth:
                    break
                depth += 1
                same_depth = next_depth
                next_depth = []
                continue
            node = same_depth.pop()
            if node is None:
                continue
            next_depth.append(node.left)
            next_depth.append(node.right)
        return depth
```

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # listになおしてとく
        # 再帰で解く
        if root is None:
            return 0
        if root.right is None and root.left is None: # これいらない
            return 1
        max_depth = max(self.maxDepth(root.left), self.maxDepth(root.right))
        return max_depth + 1
```

再帰の深さは平均logn、最悪n 10^4 程度

時間計算量はO(n)、空間は再帰分くらい

## Step2

### 同じ問題を解いた人のプルリクを見る

- https://github.com/quinn-sasha/leetcode/pull/17
    - https://github.com/quinn-sasha/leetcode/pull/17/files#r1983229930
        - 深さを足す場所の感覚
- https://github.com/t0hsumi/leetcode/pull/21
    - https://docs.python.org/ja/3/reference/simple_stmts.html#the-global-statement
        - Pythonのglobal, nonlocal
- https://github.com/olsen-blue/Arai60/pull/21
    - だいたい同じ実装をしている

### 考慮事項

- 解き方はdfs, bfs
- それぞれ再帰でもかける
- 手作業での感覚が理解しやすいコードになる

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # dfs
        # 
        if root is None:
            return 0
        stack = [(root, 1)]
        max_depth = 0
        while stack:
            node, depth = stack.pop()
            max_depth = max(max_depth, depth)# next_depthと書いてしまった
            next_depth = depth + 1
            if node.right is not None:
                stack.append((node.right,  next_depth))
            if node.left is not None:
                stack.append((node.left, next_depth))

        return max_depth   

        
            
```

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # bfs
        # 再帰
        # リストをもらって深さを返そう
        def max_depth_helper(nodes):
            if not nodes or nodes[0] is None:
                return 0
            next_nodes = []
            for node in nodes:
                if node.right is not None:
                    next_nodes.append(node.right)
                if node.left is not None:
                    next_nodes.append(node.left)
            return 1 + max_depth_helper(next_nodes)

        return max_depth_helper([root]) 
```

入力がNoneの時、落ちてしまうが関数内関数に書くか本体に書くか迷った。

幅のほうが再帰は浅くなる。logn

値が１ずれることがよくあり、脳内できちんと動かしてからRUNしようと思った。

## Step3

### 3回連続で再現できるようになる
```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        def max_depth_helper(nodes):
            if not nodes:
                return 0
            next_nodes = []
            for node in nodes:
                if node is None:
                    ValueError('nodesに不正な値があります')
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            return 1 + max_depth_helper(next_nodes)
        
        if root is None:
            return 0
        return max_depth_helper([root])
```
