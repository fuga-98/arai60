# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

BFSで解くのががよさそう。

深さを入れて判断すればよさそう

Dequeを使うときれいにかけそう。

入れるときに反転させるか、出すときに反転させるか。

いや値を入れるときに考えればよいか。

30mくらいかかった。

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        nodes = [root]
        result = []
        append_left = False

        while nodes:
            next_nodes = []
            node_values = deque()
            for node in nodes:
                assert node is not None
                if append_left:
                    node_values.appendleft(node.val)
                else:
                    node_values.append(node.val)
                
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            result.append(list(node_values))
            nodes = next_nodes
            append_left = not append_left
        return result

```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/ryoooooory/LeetCode/pull/30/files

- 再帰での解き方。
- DFSでも解けるだろうけどあんまり旨みはないと思った。
- 割と余裕出てきたのでPython以外の言語も読もうと思った

https://github.com/t0hsumi/leetcode/pull/27/files

- https://github.com/t0hsumi/leetcode/pull/27/files#r1984365150
    - nonlocal これオブジェクトの世界と名前の世界の話な気がする。
    - 束縛(https://docs.python.org/ja/3.13/reference/executionmodel.html)
    - global,nonlocal は束縛の話か、なるほど。
    - オブジェクトIDは束縛で渡す。オブジェクト自体は操作できる。
- Listを反転させるやり方もある
    - 一応見えたが、反転させるのにO(n)かかるのでなしかなと思った
    - dequeをリストに変えるのでもO(n)か。
    

https://github.com/olsen-blue/Arai60/pull/27/files

- ジグザグのフラグの持ち方が面白い
- 深さを持つか、bool値を持つか

## Step3

### 3回連続で再現できるようになる

7m

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        nodes = [root]
        result = []
        level = 1
        while nodes:
            next_nodes = []
            node_vals = []
            for node in nodes:
                assert node is not None
                node_vals.append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            if level % 2 == 0:
                node_vals.reverse()
            result.append(node_vals)
            nodes = next_nodes
            level += 1
        return result
```

```python
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        nodes = [root]
        result = []
        reverse_flag = False
        while nodes:
            next_nodes = []
            node_vals = deque()
            for node in nodes:
                assert node is not None
                if reverse_flag:
                    node_vals.appendleft(node.val)
                else:
                    node_vals.append(node.val)
                if node.left is not None:
                    next_nodes.append(node.left)
                if node.right is not None:
                    next_nodes.append(node.right)
            result.append(list(node_vals))
            nodes = next_nodes
            reverse_flag = not reverse_flag
        return result
```

