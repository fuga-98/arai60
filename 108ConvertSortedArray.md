# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

10m　バイナリツリーは再帰だと簡単にかける。

再帰の深さはlogn 10^4なので10くらい

時間は~~O(n)~~ O(nlogn)空間はO(n)

```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        # binary search Treeの定義は「左の子孫の値 ≤ 親の値 ≤ 右の子孫の値」という制約を持つ二分木である。
        # 入力を破壊しない
        # sortしてから作るか、そのまま植えていくか
        # 計算量は変わらなそう。そのまま行ってみる
        # sort済みやん
        # 二分探索
        # 再帰で解けそう
        if not nums:
            return None
        if len(nums) == 1:
            return TreeNode(nums[0])
        
        center = len(nums) // 2
        bst = TreeNode(nums[center])
        bst.left = self.sortedArrayToBST(nums[:center])
        bst.right = self.sortedArrayToBST(nums[center+1:])
        return bst
```

20m

再帰を考えてから実装すると簡単にかける気がする

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        def get_center_and_splits(nums):
            if not nums:
                return None, None, None
            if len(nums) == 1:
                return TreeNode(nums[0]), None, None
            center = len(nums) // 2
            print(center)
            return TreeNode(nums[center]), nums[:center], nums[center+1:]

        if not nums:
            return None
        bst_root, smaller, bigger = get_center_and_splits(nums)
        stack = [(bst_root, smaller, bigger)]

        while stack:
            bst, smaller, bigger  = stack.pop()
            if smaller is not None:
                bst.left, l_smaller, l_bigger = get_center_and_splits(smaller)
                stack.append((bst.left, l_smaller, l_bigger))
            if bigger is not None:
                bst.right, r_smaller, r_bigger = get_center_and_splits(bigger)
                stack.append((bst.right, r_smaller, r_bigger))
        return bst_root
                
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/t0hsumi/leetcode/pull/24/files

- スライス使うと空間計算量をとる。名前空間はlog(n)くらい使う。
    - https://docs.python.org/ja/3/library/copy.html
        - スライスはshallow copy
        - indexを持つ場合とどれくらい変わるのだろうか。
            - LeetCodeだとスライスは19.4mbとポインタは18.94mb →Memoryもあてにならない？
            - 大差ないのでスライスを使っても良い気がする。

https://github.com/colorbox/leetcode/pull/38/files

- centerとmiddle
    - 一次元に着目した時はmiddle、二次元だとcenterが自然っぽい
    - 数直線の中心はmiddle, 図形の中心はcenter
    
    > Maybe, maybe, a native speaker would be more likely to say "middle" when speaking of things arranged along a line, i.e. one dimension, and "center" when speaking of two dimensional arrangements. Like we tend to talk about the middle of a line but the center of a circle. But it wouldn't be glaring to switch them.
    > 
- 空間計算量は時間計算量を超えることはない
    - 一般的な計算モデル（例えば、ティューリングマシン）では、各計算ステップでアクセスできる記憶領域が限られているため、T(n) 回の計算を行うアルゴリズムは最大で T(n) 個のセルにしかアクセスできず、空間計算量は時間計算量の上限に収まります。
    - 時間計算量: O(N logN)
    
    ```
    auto left_nodes = vector<int>(nums.begin(), nums.begin() + center_index);
    ```
    
    でnumsの半分の長さのコピーを作成するのにO(N)時間かかる。スライスで処理が重くなるのは時間計算量か。手元での確認
    
    ```python
    b = a[1000:5000]
    スライスの平均実行時間: 1.3967312909662724 秒
    b = a[1000:10000]
    スライスの平均実行時間: 3.0429488489753567 秒
    ```
    
    https://wiki.python.org/moin/TimeComplexity
    

https://github.com/olsen-blue/Arai60/pull/24/files

- 前二つとだいたい同じ
- 型ヒントつけると読みやすい。

## Step3

### 3回連続で再現できるようになる

7m,5m,3m

```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        def sortedArrayToBST_helper(left, right):
            if left > right:
                return None
            middle = (left + right) // 2
            root = TreeNode(nums[middle])
            root.left = sortedArrayToBST_helper(left, middle-1)
            root.right = sortedArrayToBST_helper(middle+1, right)
            return root
        return sortedArrayToBST_helper(0, len(nums)-1)
```
