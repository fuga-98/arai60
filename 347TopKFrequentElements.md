# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        # 辞書で持ってソートか、ヒープキューを使う
        # ヒープキューの使い方が分からなかったので他の人の回答を軽く見た
        num_count = Counter(nums)
        frequent_q =[]
        for num in num_count:
            heappush(frequent_q, (num_count[num], num))
        while k < len(frequent_q):
            heappop(frequent_q)
        result = []
        for times,num in frequent_q:
            result.append(num)
        return result
```

## Step2

### 同じ問題を解いた人のプルリクを見てレビューする

- Counterのメソッドを使えば一行で書ける
    - https://docs.python.org/ja/3.13/library/collections.html#collections.Counter

```python
class Solution:
    def topKFrequent(self, nums, k):
        return [num for num, _ in Counter(nums).most_common(k)]
```

- DeafaultDict関数を使えば初期値に０が入る。
    - Lambdaを使ったソートをあまり理解できていない

```python
class Solution:
    def topKFrequent(self, nums, k):
        num_count = defaultdict(int)
        for num in nums:
            num_count[num] += 1
        sorted_count = sorted(num_count.items(), key=lambda x:x[1], reverse=True) # Tuple
        return [num for num, _ in sorted_count][:k]
```

- ドキュメント

https://docs.python.org/ja/3.13/library/stdtypes.html#mapping-types-dict

https://docs.python.org/ja/3.13/library/collections.html#counter-objects

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_count = Counter(nums)
        result = [(count, num) for num, count in num_count.items()]
        heapify(result)
        while len(result) > k:
            heappop(result)
        return [num for _, num in result]
        
```
