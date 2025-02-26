# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

書いたがWrongAnswerになった

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        sum_num1_num2 = []
        # 全部作ってKまで返す
        for num1 in nums1:
            for num2 in nums2:
                heappush(sum_num1_num2, (-(num1 + num2), num1, num2))
        while len(sum_num1_num2) > k:
            heappop(sum_num1_num2)
        return [(num1,num2) for _, num1, num2 in sum_num1_num2]
```

Memory Limit Exceededした

最小のどちらかは含まれるんじゃないかと思ってやってみた

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        sum_num1_num2 = []
        # kまで作る
        result = []
        while True:
            # num1の最小
            min1 = heappop(nums1)
            for num2 in nums2:
                heappush(result, (-(min1 + num2), min1, num2))
            min2 = heappop(nums2)
            for num1 in nums1:
                heappush(result,(-(num1 + min2), num1, min2))
            if len(result) >= k:
                break
        while len(result) > k:
            heappop(result)
        return [(num1,num2) for _, num1, num2 in result]  
```

## Step2

### 同じ問題を解いた人のプルリクを見る

この発想は初見だと難しい

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        candidate = []
        checked = set()
        def add_candidate(i, j):
            if (i,j) in checked:
                return
            if len(nums1) <= i or len(nums2) <= j:
                return
            checked.add((i, j))
            heappush(candidate, (nums1[i] + nums2[j], i, j))
        add_candidate(0,0)
        result = []
        while True:
            if not candidate or len(result) >= k:
                break
            _, i,j = heappop(candidate)
            result.append((nums1[i], nums2[j]))
            add_candidate(i + 1, j)
            add_candidate(i, j + 1)
        return result

```

先に(i,0)を入れて、(i, k)が最小なら(i,k + 1)を入れる

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        candidate = []

        def push_candidate(i, j):
            if len(nums1) <= i or len(nums2) <= j:
                return
            heappush(candidate, (nums1[i] + nums2[j], i, j))

        for i, _ in enumerate(nums1):
            push_candidate(i, 0)
        result = []
        while len(result) < k:
            _, i, j = heappop(candidate)
            heappush(result, (nums1[i], nums2[j]))
            push_candidate(i, j + 1)
        return result

```

座標を意識すると理解しやすい

### 気づき

直近で解いた人達は解法、変数名が似る気がする。

理解しやすい反面、ソースを読む練習にはなりにくいかもしれない。

過剰に適応しないように、少し古いプルリクやコメント集を読むのも重要だと感じた。

## Step3

### 3回連続で再現できるようになる

先に(i,0)を入れて、(i, k)が最小なら(i,k + 1)を入れるで解いた

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        
        def add_candidate(i, j):
            if len(nums1) <= i or len(nums2) <= j:
                return
            heappush(candidate, (nums1[i] + nums2[j], i, j))
        
        candidate = []
        for i, _ in enumerate(nums1):
            add_candidate(i, 0)
        result = []
        while candidate and len(result) < k:
            _, i, j = heappop(candidate)
            result.append([nums1[i], nums2[j]])
            add_candidate(i, j + 1)
        return result
```
