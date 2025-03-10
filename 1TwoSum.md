# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # 総当たりすればいける、でもO(N^2)
        # setを使えば時間計算量、空間計算量はO(N)で行けるかも。
        nums_set = set(nums)
        for i, num in enumerate(nums):
            match_num = target - num
            if match_num not in nums_set:
                continue
            j = nums.index(match_num)
            if j != i:
                return (i,j) 
```

## Step2

### 同じ問題を解いた人のプルリクを見る

- 例外処理を作成している人が多かった印象

こういう問題以外のところにも気を配りたい…

https://docs.python.org/ja/3.13/library/exceptions.html#

- Two pointers

```
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # Two pointer
        sorted_nums = sorted(nums)
        right = 0
        left = len(sorted_nums) - 1
        while right < left:
            total = sorted_nums[right] + sorted_nums[left]
            if total < target:
                right += 1
                continue
            if total > target:
                left -= 1
                continue
            # total == target
            if sorted_nums[right] == sorted_nums[left]:
                index1 = nums.index(sorted_nums[right])
                index2 = nums.index(sorted_nums[left], index1 + 1)
            else:
                index1 = nums.index(sorted_nums[right])
                index2 = nums.index(sorted_nums[left])
            return [index1, index2]
            
        raise ValuerError('Each input would have exactly one solution.')
```

- binary searchでも解ける

https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.c15qprmvxkc2

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        nums_set = set(nums)
        for i, num in enumerate(nums):
            complement = target - num
            if complement not in nums_set:
                continue
            j = nums.index(complement)
            if i != j:
                return [i, j]
        
        raise ValueError('No valid pair of numbers found in the input list.')
```
