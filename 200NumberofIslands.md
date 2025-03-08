# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        # 手作業でやるなら、、、縦と横が一緒であれば同じグループっぽい
        # 一つのlandを見つけたら隣接する1を探すか。setに保存していって
        visited = set()

        def search_island(i, j):
            """
            探索済みか海なら0を返却
            landなら隣接するGridすべて探索し、1を返却
            """
            if (i, j) in visited:
                return 0
            if (i < 0 or len(grid) <= i 
                    or j < 0 or len(grid[0]) <= j):
                return 0
            visited.add((i, j))
            if grid[i][j] == "0":
                return 0
            _ = search_island(i + 1, j)
            _ = search_island(i - 1, j)
            _ = search_island(i, j + 1)
            _ = search_island(i, j - 1)
            return 1
        
        result = 0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                result += search_island(i, j)

        return result

           
```

## Step2

### 同じ問題を解いた人のプルリクを見る

## DFS

https://github.com/Fuminiton/LeetCode/pull/17/files

- 解き方は自分と近い
- マジックナンバーを避ける
- 再帰が深くなる場合は再帰以外で書いたほうが良い
- 行列は(i, j)ではなく(row, column) が良さそう

https://github.com/olsen-blue/Arai60/pull/17/files

- 入力を破壊する実装
- 基本入力の破壊はしないほうが良いだろうが、入力がとても大きいときはありなのかもしれない

## UnionFind

```python
class Solution:

    class UnionFind:
        def __init__(self, coodinates: tuple[int, int]):
            self.parent = {}
            for coodinate in coodinates:
                    self.parent[coodinate] = coodinate
            
        def find(self, coodinate: tuple[int, int]):
            """rootを見つける"""
            # 圧縮する
            if self.parent[coodinate] == coodinate:
                return self.parent[coodinate]
            return self.find(self.parent[coodinate])

            # while True:
            #     if self.parent[coodinate] == coodinate:
            #         return coodinate
            #     self.parent[coodinate] = self.parent[self.parent[coodinate]]
            #     coodinate = self.parent[coodinate] 
        
        def union(self, right: tuple[int, int], left: tuple[int, int]):
            # rankは実装しない
            right_parent = self.find(right)
            left_parent = self.find(left)

            if right_parent == left_parent:
                return
            # ここで圧縮しなくて大丈夫かな
            self.parent[right_parent] = left_parent

    def numIslands(self, grid: List[List[str]]) -> int:
        WATER = '0'
        LAND = '1'
        row_size = len(grid)
        column_size = len(grid[0])
        lands = set()

        for row in range(row_size):
            for column in range(column_size):
                if grid[row][column] == LAND:
                    lands.add((row,column))
        
        uf = self.UnionFind(lands)
        
        for row, column in lands:
            coodinate = (row, column)
            if(row + 1, column) in lands:
                uf.union(coodinate, (row + 1, column))
            if (row, column + 1) in lands:
                uf.union(coodinate, (row, column + 1))
        
        result = 0
        for coodinate, parent in uf.parent.items():
            if coodinate == parent:
                result += 1
            
        return result
```

https://github.com/ichika0615/arai60/pull/9/files

- スコープの短い定数を大文字で表現すべきか。
- PEP8には書いていないが、ファイル内の定数を書き換える可能性があるから避けたほうが良いのかなと思っている。

> 定数は通常モジュールレベルで定義します。全ての定数は大文字で書き、単語をアンダースコアで区切ります。例として `MAX_OVERFLOW` や `TOTAL` があります。
> 
- https://pep8-ja.readthedocs.io/ja/latest/#id38

https://github.com/t0hsumi/leetcode/pull/18/files

- メンバ変数にcountを持っている
- countメソッドとして定義してもよいなと思った。
- https://en.wikipedia.org/wiki/Disjoint-set_data_structure#Finding_set_representatives
- 計算量はそのうち読みたい

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        LAND = '1'
        num_row = len(grid)
        num_column = len(grid[0])
        visited = set()
        
        def is_new_land(row, column):
            if row < 0 or num_row <= row:
                return False
            if column < 0 or num_column <= column:
                return False
            if grid[row][column] != LAND:
                return False
            if (row, column) in visited:
                return False
            return True

        def traverse(row, column):
            stack = []
            stack.append((row, column))
            while stack:
                stack_r, stack_c = stack.pop()
                for d_r, d_c in ((0,0),(0,1),(1,0),(-1,0),(0,-1)):
                    r = stack_r + d_r
                    c = stack_c + d_c
                    if not is_new_land(r, c):
                        continue
                    visited.add((r,c))
                    stack.append((r,c))

        result = 0
        for row in range(num_row):
            for column in range(num_column):
                if is_new_land(row, column):
                    result += 1
                    traverse(row,column)
        
        return result
```

