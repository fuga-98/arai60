# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

bfs で全探索。二重ループで書くときれいにかけそう。

時間計算量はO(n)、空間計算量は同level分なのでO(logn)

2000くらいなので2msくらい

10分くらい。間違えた個所はwhileの終了条件の書き忘れ、

assertはFalseの時にAssertionErrorを出す

stackではないことに気づいた

```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        stack = [root]
        result = []

        while True:
            next_nodes = []
            node_vals = []
            for node in stack:
                assert node is not None
                node_vals.append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            result.append(node_vals)
            if not next_nodes:
                return result 
            stack = next_nodes
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/t0hsumi/leetcode/pull/26

- DFSでも解ける。あんまりメリットはなさそう
- level_ordered_values[level].append(node.val)こういう書き方もある

https://github.com/SuperHotDogCat/coding-interview/pull/42/files

- 変数名が長く感じるけど、ここまで正確に表現するべきなのだろうか
- プロジェクトに合わせる感じにはなるだろうけども

https://github.com/olsen-blue/Arai60/pull/26/files

- デッドコード、、これで何回かバグ出たな

## Step3

### 3回連続で再現できるようになる

BFSを三回
```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        nodes = [root]
        level_order = []
        while True:
            next_nodes = []
            level_values = []
            for node in nodes:
                assert node is not None
                level_values.append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            level_order.append(level_values)
            if not next_nodes:
                return level_order
            nodes = next_nodes
```
