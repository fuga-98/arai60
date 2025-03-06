# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

マイナスが入る場合が解けない

→二回ループしているので順番の概念がなくなっており、差を絶対値でとっている

他の人のものを見る

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        # listの先頭をずらしていくか
        # でもこれだと全部見ないといけないから、len(nums) ^ 2　/ 2 だ。
        # 1000万くらいか、、マイナスがあるから難しいんだよなあ
        # 累積和みたいなことかも？
        # 計算量は　len(nums) * 2 すればいけそう
        runnning_totals = []
        total = 0

        for num in nums:
            total += num 
            runnning_totals.append(total)
        
        total_to_count = Counter(runnning_totals)
        print(total_to_count)
        target = k
        result = 0

        for num in nums:
            if total_to_count[target] is None:
                target += num
                continue
            result += total_to_count[target]
            target += num
            print(result)
        
        return result
```

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum_count = defaultdict(int)
        prefix_sum_count[0] = 1  # 累積和が 0 の場合を考慮
        prefix_sum = 0
        result = 0

        for num in nums:
            prefix_sum += num  # 現在の累積和を更新

            # 過去に (prefix_sum - k) の累積和があれば、それを加算
            if (prefix_sum - k) in prefix_sum_count:
                result += prefix_sum_count[prefix_sum - k]

            # 現在の累積和を辞書に記録
            prefix_sum_count[prefix_sum] += 1

        return result
```

## Step2

### 同じ問題を解いた人のプルリクを見る

- prefix_sum_count[prefix_sum - k]の意味が分からなかった。
    - 式変形すると理解できた、差分がｋとなる値の出現回数を加える
    - x = prefix_sum - k →  k = prefix_sum - x
- 鉄道があって、各駅間ごとの標高差が与えられます。標高差が ちょうど K であるようなすべての駅の組み合わせを列挙してください。
    - この例えだと、標高と駅の辞書。今の駅の高さとkだけ低い駅を探したい

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)
        prefix_sum_to_count[0] = 1
        result = 0

        for num in nums:
            prefix_sum += num
            target = prefix_sum - k
            result += prefix_sum_to_count[target]
            prefix_sum_to_count[prefix_sum] += 1

        return result  
```

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)
        prefix_sum_to_count[0] = 1
        result = 0

        for num in nums:
            prefix_sum += num
            result += prefix_sum_to_count[prefix_sum - k]
            prefix_sum_to_count[prefix_sum] += 1
        
        return result
```
