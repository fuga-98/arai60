# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

1時間くらいかかった。

node1がNoneになったときにどうすればよいかが思いつかなかった。

node2をそのままつなげてしまえばよかったのか。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        # 解き方は二つを探索して足す
        # dfsがわかりやすそう→書けなかった
        # 時間計算量はO(n)　空間は深さ分O(logn)
        # 最大値は2000個だから2000/100万で2/1000秒くらい
        # stackに二つ入れてしまうか。
        if root1 is None:
            return root2

        nodes = [(root1, root2)]
        while True:
            next_nodes = []
            while nodes:
                node1, node2 = nodes.pop()
                if node2 is None:
                    continue
                node1.val += node2.val

                if node1.right is None:
                    node1.right = node2.right
                else:
                    next_nodes.append((node1.right, node2.right))
                
                if node1.left is None:
                    node1.left = node2.left
                else:
                    next_nodes.append((node1.left, node2.left))
                
            if not next_nodes:
                return root1
            nodes = next_nodes
            
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/olsen-blue/Arai60/pull/23/files

- 思考過程が書いてあって読みやすい。
- 再帰で書くのはよさそう。
- 変数を破壊しない＋変数を共有しないことは考えたい

https://github.com/t0hsumi/leetcode/pull/23

- https://docs.python.org/3/library/functions.html#filter
    - filter関数覚えておこう
    - 英語への抵抗感があるので、勉強しなおしたい
- 関数化すると読みやすい。覚えておくことが減るので。

https://github.com/ryoooooory/LeetCode/pull/26/files

- javaが少し苦手
- 条件分岐を減らす、番兵

再帰で書く

7分

```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        # 入力を破壊しない再帰
        if root1 is None and root2 is None:
            return None
        if root1 is None:
            return TreeNode(val=root2.val, left=root2.left, right=root2.right)
        if root2 is None:
            return TreeNode(val=root1.val, left=root1.left, right=root1.right)
        result_val = root1.val + root2.val
        result_left = self.mergeTrees(root1.left, root2.left)
        result_right = self.mergeTrees(root1.right, root2.right) 
        return TreeNode(val=result_val, left=result_left, right=result_right)
```

再帰を使わないDFS

WrongAnswer 

https://dodotechno.com/python-call-by-value/

おそらく代入のスキーマが間違えているからこの書き方をしている

> ここでの *値 (value)* とは常にオブジェクトへの *参照(reference)* をいい、オブジェクトの値そのものではありません)

https://docs.python.org/ja/3/tutorial/controlflow.html#defining-functions
> 

```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        # 再帰を使わないdfs
        if root1 is None and root2 is None:
            return None
        if root1 is None:# いらない気がする
            root1 = TreeNode()
        if root2 is None:
            root2 = TreeNode()

        new_root = TreeNode()
        stack = [(root1, root2, new_root)]

        while stack:
            node1, node2, new_node = stack.pop()
            if node1 is None and node2 is None:
                continue
            if node1 is None:
                node1 = TreeNode()
            if node2 is None:
                node2 = TreeNode()
            nodes_sum = node1.val + node2.val
            new_node = TreeNode(val=nodes_sum)
            stack.append((node1.right, node2.right, new_node.right))
            stack.append((node1.left, node2.left, new_node.left))

        return new_root
```

```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        def merge_nodes(node1, node2):
            if node1 is None and node2 is None:
                return None
            if node1 is None:
                node1 = TreeNode()
            if node2 is None:
                node2 = TreeNode()
            sum_vals = node1.val + node2.val
            return TreeNode(val=sum_vals)
        
        # 再帰を使わないdfs        
        merged_root = merge_nodes(root1, root2)
        stack = [(root1, root2, merged_root)]

        while stack:
            node1, node2, merged_node = stack.pop()
            if node1 is None and node2 is None:
                continue
            if node1 is None:
                node1 = TreeNode() # 入力を破壊していると思った
            if node2 is None:
                node2 =  TreeNode()

            merged_node.left = merge_nodes(node1.left, node2.left)
            merged_node.right = merge_nodes(node1.right, node2.right)
            stack.append((node1.left, node2.left, merged_node.left))
            stack.append((node1.right, node2.right, merged_node.right))

        return merged_root

```

間違ってListNodeと書いたらこんなエラーが出た。この問題には関係ないListNodeクラスがあるっぽい。実行環境は共通なのかも。

AttributeError: 'ListNode' object has no attribute 'right’

## Step3

### 3回連続で再現できるようになる

15m

10m

7m

同じようなifを書いているのが気に入らないが、いい案が思いつかない
```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:

        def merge_nodes(node1, node2):
            if node1 is None and node2 is None:
                return None
            if node1 is None:
                node1 = TreeNode()
            if node2 is None:
                node2 = TreeNode()
            return TreeNode(val=node1.val+node2.val)
        
        merged_root = merge_nodes(root1, root2)
        stack = [(root1, root2, merged_root)]
        while stack:
            node1, node2, merged_node = stack.pop()
            if node1 is None and node2 is None:
                continue
            if node1 is None:
                node1 = TreeNode()
            if node2 is None:
                node2 = TreeNode()
            merged_node.left = merge_nodes(node1.left, node2.left)
            merged_node.right = merge_nodes(node1.right, node2.right)
            stack.append((node1.left, node2.left, merged_node.left))
            stack.append((node1.right, node2.right, merged_node.right))
        
        return merged_root
```
