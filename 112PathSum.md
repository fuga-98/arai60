# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

時間計算量はO(n) 空間計算量はO(logn)

再帰の深さはlogn　 n= 5000 なので余裕がある

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int,total: int=0) -> bool:
        # 再帰が簡単そう
        if root is None:
            return False
        total += root.val
        if root.right is None and root.left is None:
            return total == targetSum
        left_result = self.hasPathSum(root.left, targetSum, total)
        right_result = self.hasPathSum(root.right, targetSum, total)
        return left_result or right_result
```

終わりまで見ないといけないのでDFSでよさそう。

これの解き方の幅あるかな。

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/ryoooooory/LeetCode/pull/28/files

- 直線に並ぶと再帰が5000になる

https://github.com/t0hsumi/leetcode/pull/25/files

- https://github.com/t0hsumi/leetcode/pull/25/files#r1981057519
    - 改行が減ったの少しわかる

https://github.com/olsen-blue/Arai60/pull/25/files

- バグがあったときに隠蔽しない

そこまで幅はなさそう

## Step3

### 3回連続で再現できるようになる
```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        total = 0
        stack = [(root, total)]
        while stack:
            node, total = stack.pop()
            if node is None:
                continue
            total += node.val
            if node.left is None and node.right is None:
                if total == targetSum:
                    return True
            # NoneでもStackに入れる
            stack.append((node.left, total))
            stack.append((node.right, total))
        return False
```
