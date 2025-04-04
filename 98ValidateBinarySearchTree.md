# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

↓問題の読解に失敗しています

全探索すればよさそう。

DFSとBFSはそこまで変わらなそうだ

時間はどちらもO(n)

空間はDFSは左右両側についているものだと、深さ分なのでO(logn)

BFSだと一番深いところがO(logn)

DFSで書く。10^4 / 10^6 = 0.02秒くらい

binary Treeは閉区間なのかな

不正解です↓

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if root is None:
            return True
        stack = [root]
        while stack:
            node = stack.pop()
            assert node is not None
            if node.left is None and node.right is None:
                continue
            if node.left is None:
                if node.val >= node.right.val:
                    return False
                stack.append(node.right)
                continue
            if node.right is None:
                if node.left.val >= node.val:
                    return False
                stack.append(node.left)
                continue
            if not (node.left.val < node.val < node.right.val):
                return False
            stack.append(node.left)
            stack.append(node.right)
        return True
            
```

枝ごとにいきたいのでDFSがよさそう。

時間計算量はO(n) 空間は深さ分O(logn)

再帰はまっすぐつながっている場合の10^4

再帰リミットはデフォルト1000, leetcodeは55000

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def isValidBST_helper(root, lower_bound, upper_bound):
            assert root is not None
            if not (lower_bound < root.val < upper_bound):
                return False
            if root.left is not None:
                upper_bound_left = min(upper_bound, root.val) # upper_boundとしてしまうミスした
                if not isValidBST_helper(root.left, lower_bound, upper_bound_left):
                    return False
            if root.right is not None:
                lower_bound_right = max(lower_bound, root.val)
                if not isValidBST_helper(root.right, lower_bound_right, upper_bound):
                    return False
            return True
        
        if root is None:
            return True
        return isValidBST_helper(root, -inf, inf)
```

再帰を書き換えるのはできそう。stackに(node, 下界, 上界)を入ればいけそう

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/olsen-blue/Arai60/pull/28/files

- めっちゃ簡単に書いている、、、これは二分探索木の性質を知らないから書けなかったかな。
    - 終了条件をnode is Noneにしているのか。Noneを入れないほうがシンプルに書ける
    - 二分探索木の使う場面を考えるとしっくりきた
- これは結構解法があるっぽい
    
    上から範囲の制約を下げていくか、下から値の範囲を上げていくか、インオーダーで出力して昇順になっているか、などでしょうか。
    
    →要は順序が守られるようにとっていく感じか。
    
    ![image.png](attachment:8456bb1c-02cf-465f-9849-e2507e8c1e8a:image.png)
    
- 最大値の番兵の置き方。floatのinfを使うか、sys.maxsizeを使うか。Noneで分岐するか

https://github.com/colorbox/leetcode/pull/42/files

- 並列性を意識する。
    - https://discord.com/channels/1084280443945353267/1201211204547383386/1247145320098566144

https://github.com/SuperHotDogCat/coding-interview/pull/41

- Phase5を読むのが難しかった
- 再帰関数の中で再帰関数を使うとけっこう複雑になる
- 二重ループで書くほうが覚えておかないといけない内容が少ない気がする

配列で出力して解く。

分けたほうが分かりやすいかもしれない。

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        val_list = []
        def construct_val_list(root):
            if root is None:
                return 
            construct_val_list(root.right)
            val_list.append(root.val)
            construct_val_list(root.left)
        
        construct_val_list(root)
        high = math.inf
        for node_val in val_list:
            if node_val >= high:
                return False
            high = node_val
        return True
```

## Step3

### 3回連続で再現できるようになる

それぞれのやり方で解いた。コンセプトさえ分かっていれば再現できる感覚になってきた。

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def helper(root, min_val, max_val):
            if root is None:
                return True
            if not (min_val < root.val < max_val):
                return False
            return helper(root.left, min_val, root.val) and helper(root.right, root.val, max_val)
        
        return helper(root, -math.inf, math.inf)
```

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if root is None:
            return True
        stack = [(root, -math.inf, math.inf)]
        while stack:
            node, min_val, max_val = stack.pop()
            if not (min_val < node.val < max_val):
                return False
            if node.left is not None:
                stack.append((node.left, min_val, node.val))
            if node.right is not None:
                stack.append((node.right, node.val, max_val))
        return True
```

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        order_list = []
        def make_order_list(root):
            if root is None:
                return 
            make_order_list(root.left)
            order_list.append(root.val)
            make_order_list(root.right)
        
        make_order_list(root)
        prev = -math.inf
        for val in order_list:
            if val <= prev:
                return False
            prev = val
        return True
```
