# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # setでよさそう　というか積の関数が多分ある気がする
        # 計算ステップは、4000ステップくらい
        set1 = set(nums1)
        set2 = set(nums2)
        result = list()
        for num1 in set1:
            if num1 not in set2:
                continue
            result.append(num1)
        return result
            
            
 class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:

        set1 = set(nums1)
        set2 = set(nums2)
        return list(set1 & set2)
```

## Step2

### 同じ問題を解いた人のプルリクを見る

- Intersectionの実装を見る

https://github.com/python/cpython/blob/f97e4098ff71a6488fd3411f9f9e6fa7a7bb4efe/Objects/setobject.c#L1363

追加質問が想定されるみたい。

- 片方がとても大きくて、片方がとても小さいときには、大きい方を set にするのは大変じゃないでしょうか、特に大きいほうが sort 済みのときにはどうしますか。

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # 大きいソート済み配列が渡されることを想定して、マージでかく
        # 計算量は小さいほうの配列サイズをｎとするとO(n)
        # この想定だと答えも大きくなるのでresultはsetではなく、list型で持ったほうがよい
        sorted1 = sorted(nums1)
        sorted2 = sorted(nums2)
        index1, index2 = 0, 0
        result = list()

        while True:
            if index1 >= len(sorted1) or index2 >= len(sorted2):
                break
            num1 = sorted1[index1]
            num2 = sorted2[index2]
            if num1 == num2:
                if not result or num1 != result[-1]: 
                    result.append(num1)
                index1 += 1
                index2 += 1
                continue
            if num1 < num2:
                index1 += 1
                continue
            index2 += 1
        
        return result
```

## Step3

### 3回連続で再現できるようになる
```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        MAX = sys.maxsize
        sorted1 = sorted(nums1)
        sorted2 = sorted(nums2)
        sorted1.append(MAX)
        sorted2.append(MAX)
        result = list()
        i, j = 0, 0

        while True:
            num1 = sorted1[i]
            num2 = sorted2[j]
            if num1 == MAX or num2 == MAX:
                return result # ここでreturnしたら違和感あるだろうか
            if num1 == num2:
                if not result or  num1 != result[-1]:
                    result.append(num1)
                i += 1
                j += 1
                continue
            if num1 < num2:
                i += 1
                continue
            j += 1
```
