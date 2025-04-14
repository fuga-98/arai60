# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ



```python
LAND = 1

class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_columns = len(grid[0])
        visited = set()
        
        def is_new_land(row, column):
            if not (0 <= row < num_rows):
                return False
            if not (0 <= column < num_columns):
                return False
            if grid[row][column] != LAND:
                return False
            if (row, column) in visited:
                return False
            return True
        
        def visit_and_get_area(row, column):
            if not is_new_land(row, column):
                return 0

            stack = []
            stack.append((row, column))
            visited.add((row, column))
            direction = ((1,0),(0,1),(-1,0),(0,-1))
            area = 1

            while stack:
                stack_r, stack_c = stack.pop()
                for d_r, d_c in direction:
                    r = stack_r + d_r
                    c = stack_c + d_c
                    if not is_new_land(r, c):
                        continue
                    area += 1
                    visited.add((r, c))
                    stack.append((r, c))
            print(area)   
            return area

        max_area = 0

        for row in range(num_rows):
            for column in range(num_columns):
                if not is_new_land(row, column):
                    continue
                area = visit_and_get_area(row, column)
                max_area = max(max_area, area)
        
        return max_area

        
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/Fuminiton/LeetCode/pull/18

- 再帰の書き方、変数名ともに読みやすい

https://github.com/t0hsumi/leetcode/pull/19

- 土地の大きさを数える関数で、外部の変数を変更するのはどうなのだろうか
    - 副作用https://github.com/olsen-blue/Arai60/pull/29#discussion_r1948645912
        - 今回は狭い関数内関数という狭いスコープで、副作用のあるほうがシンプルにかけるので、そっちを選択する

https://github.com/olsen-blue/Arai60/pull/18

- 条件に否定があると読みにくいと感じる人がいるみたい
- 引数内で関数を呼ぶと、認知負荷高い
- bfsとdfsの違いがあまり分かっていない

https://github.com/katataku/leetcode/pull/17

- start_row,start_column は分かりやすいと思った

### この問題で考えるべきこと

方法1：dfs, bfs

気にすること：再帰の上限、入力の破壊の可否

方法2：UnionFind

気にすること：最適化手法（union by rank, path compression

UnionFindで解く　40分くらいかかった。この量を書くならエディタを使いたくなった、、、

```python
LAND = 1
class Solution:
    class UnionFind:
        def __init__(self,lands: set[tuple[int, int]]):
            self.parent = {}
            self.rank = {}
            self.area = {}
            for land in lands:
                self.parent[land] = land
                self.rank[land] = 1
                self.area[land] = 1

        def find(self, coodinate):
            parent = self.parent[coodinate]   
            if self.parent[coodinate] == coodinate:
                return self.parent[coodinate]
            # 親の親
            self.parent[coodinate] = self.find(self.parent[coodinate])
            return self.parent[coodinate]

        def union(self, x: tuple[int, int], y: tuple[int, int]):
            '''親でないareaは0とする'''
            x_parent = self.find(x)
            y_parent = self.find(y)
            if (x_parent == y_parent):
                return
            
            if self.rank[x_parent] == self.rank[y_parent]:
                self.rank[x_parent] += 1
                self.parent[y_parent] = x_parent
                self.area[x_parent] += self.area[y_parent]
                self.area[y_parent] = 0
                return 
            
            if self.rank[x_parent] > self.rank[y_parent]:
                self.parent[y_parent] = x_parent
                self.area[x_parent] += self.area[y_parent]
                self.area[y_parent] = 0
                return 

            self.parent[x_parent] = y_parent
            self.area[y_parent] += self.area[x_parent]
            self.area[x_parent] = 0

    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        lands = set()
        num_rows = len(grid)
        num_columns = len(grid[0])
        for row in range(num_rows):
            for column in range(num_columns):
                if grid[row][column] == LAND:
                    lands.add((row, column))
        
        if not lands:
            return 0 

        uf  = self.UnionFind(lands)
        for row, column in lands:
            coodinate = (row, column)
            if(row + 1, column) in lands:
                uf.union(coodinate, (row + 1, column))
            if (row, column + 1) in lands:
                uf.union(coodinate, (row, column + 1))

        return max(uf.area.values())
            
```

## Step3

### 3回連続で再現できるようになる

1回目:17分

2回目:9分

3回目:10分

どんな変数を使うか考えずに書き始めてしまいがち

```python
LAND = 1
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        num_rows = len(grid)
        num_columns = len(grid[0])
        visited = set()
        
        def is_new_land(row, column):
            if not (0 <= row < num_rows and 0 <= column < num_columns):
                return False
            if grid[row][column] != LAND:
                return False
            if (row, column) in visited:
                return False
            return True
        
        def calculate_area(row,column):
            if not is_new_land(row, column):
                return 0
            visited.add((row,column))
            result = 1
            result += calculate_area(row + 1, column)
            result += calculate_area(row - 1, column)
            result += calculate_area(row, column + 1)
            result += calculate_area(row, column - 1)
            return result
        
        max_area = 0
        for row in range(num_rows):
            for column in range(num_columns):
                if is_new_land(row, column):
                    area = calculate_area(row, column)
                    max_area = max(max_area, area)
        
        return max_area    
```

