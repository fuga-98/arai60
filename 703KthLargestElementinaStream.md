# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照する。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

ヒープキューを知らなかったので、リストで書いた

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        # 多分効率悪いけどsortすればいけそう
        self.sort_list = sorted(nums, reverse=True)
        self.kth = k - 1 # 変数名わからん

    def add(self, val: int) -> int:
        self.sort_list.append(val)
        self.sort_list.sort(reverse=True)
        return self.sort_list[self.kth]
```

ヒープキューを調べて解いた。if分岐がうまく書けなかった

もう少しシンプルになりそう

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        heapq.heapify(nums)
        self.kth_highest = nums
        self.k = k
        for i in range(len(nums)-k):
            if len(self.kth_highest) == 1:
                break
            heapq.heappop(self.kth_highest)
            
    def add(self, val: int) -> int:
        if len(self.kth_highest) < self.k:
            heapq.heappush(self.kth_highest, val)
        else:
            heapq.heappushpop(self.kth_highest, val)
        return self.kth_highest[0]
```

## Step2

### 同じ問題を解いた人のプルリクを見る、レビューをする

- 入力を破壊しない
- 初期化の時にaddを呼び出すとスマートにかける
- topk_scoresという変数名良い
- ソートを崩さず挿入できるものがある
    - https://docs.python.org/3/library/bisect.html#bisect.insort_right

heapを用いた書き方

```python
class KthLargest:

    def __init__(self, k: int, scores: List[int]):
        self.k = k
        self.topk_scores = []
        for score in scores:
            self.add(score)
        
    def add(self, score: int) -> int:
        if len(self.topk_scores) < self.k:
            heappush(self.topk_scores, score)
        else:
            heappushpop(self.topk_scores, score)
        return self.topk_scores[0]
```

insort割と早い。Heapとそこまで変わらない（普通にappendするより5倍くらい速い）

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.sort_list = sorted(nums)
        # リストは昇順なので正負を反転
        self.k = -k 

    def add(self, val: int) -> int:
        insort(self.sort_list, val)
        return self.sort_list[self.k]
```

## Step3

### 3回連続で再現できるようになる

K個入る箱を用意して、溢れなければ入れるだけ、溢れたら入れてとる

```python
class KthLargest:

    def __init__(self, k: int, scores: List[int]):
        self.k = k
        self.top_scores = []
        for score in scores:
            self.add(score)

    def add(self, score: int) -> int:
        if len(self.top_scores) < self.k:
            heappush(self.top_scores, score)
            return self.top_scores[0]
        heappushpop(self.top_scores, score)
        return self.top_scores[0]
```

ソート配列

```python
class KthLargest:

    def __init__(self, k: int, scores: List[int]):
        # kth_highestとどっちがよいだろうか
        self.kth_right = -k
        self.sorted_scores = sorted(scores)

    def add(self, score: int) -> int:
        insort(self.sorted_scores, score)
        return self.sorted_scores[self.kth_right]
```

あ
