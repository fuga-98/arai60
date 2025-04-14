# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

偶数、奇数みたいな？10,0,0,10 みたいなのがあったらだめか。

DPで、k + dp[k - 2] が最大値といえそう。いやdp[k-1]が最大値の場合もあるか。

時間、空間ともにO(N)

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        num_houses = len(nums)
        if num_houses == 1: # ここ忘れてWA
            return nums[0]
        max_money = [0] * num_houses
        max_money[0] = nums[0]
        max_money[1] = max(nums[0], nums[1])
        for i in range(2, num_houses):
            candidate = nums[i] + max_money[i-2]
            max_money[i] = max(max_money[i-1], candidate)
        return max_money[-1]
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/olsen-blue/Arai60/pull/35

- トップダウンとボトムアップ
- 感情をめっちゃ書いていて記憶に残りやすい

https://github.com/hroc135/leetcode/pull/33/files

- トップダウンがフィボナッチになるという話
- 抽象的なものに人格を感じる
    - わからなくもない。記号接地している状態ということ？

https://github.com/TORUS0818/leetcode/pull/37/files

- 変数名の長さ
- ローカル変数はそんなに長くしなくてもよいかな、という気持ちになった。
- コメント、docstring、変数名を取捨選択しながら分かりやすいコードを書くのがよいなあと思った。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        @cache
        def get_max_count(index):
            if index == 0:
                return nums[0]
            if index == 1:
                return max(nums[0], nums[1])
            candidate = get_max_count(index-2) + nums[index]
            return max(candidate, get_max_count(index-1))
        return get_max_count(len(nums)-1)
```

これ、二回robがあったら壊れる？

↓壊れた。cacheは純粋関数に使いたいかも。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        @cache
        def get_max_count(index):
            if index == 0:
                return nums[0]
            if index == 1:
                return max(nums[0], nums[1])
            candidate = get_max_count(index-2) + nums[index]
            return max(candidate, get_max_count(index-1))
        temp = nums
        nums = [1,3, 5]
        print(get_max_count(len(nums)-1))
        nums = temp
        return get_max_count(len(nums)-1)
```

↓

　　　　　　　これは通る。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        test = [1,2,3,100]
        self._rob(test)
        return self._rob(nums)

    def _rob(self, nums: List[int]) -> int:
        @cache
        def get_max_count(index):
            if index == 0:
                return nums[0]
            if index == 1:
                return max(nums[0], nums[1])
            candidate = get_max_count(index-2) + nums[index]
            return max(candidate, get_max_count(index-1))
        return get_max_count(len(nums)-1)
```

### Decorator

https://discord.com/channels/1084280443945353267/1215189047161659464/1229701450288857089

デコレーターは関数を引数にとって関数を返す関数

Decoratorドキュメント

https://docs.python.org/ja/3.13/glossary.html

関数定義

https://docs.python.org/ja/3.13/reference/compound_stmts.html#function

functools

https://docs.python.org/ja/3.13/library/functools.html

結果のキャッシュには辞書が使われるので、関数の位置引数およびキーワード引数は [ハッシュ可能](https://docs.python.org/ja/3.13/glossary.html#term-hashable) でなくてはなりません。

Cacheの実装

https://github.com/python/cpython/blob/3.13/Lib/functools.py#L677

https://github.com/python/cpython/blob/3.13/Lib/functools.py#L577

関数がどのように解釈されるかを理解していないのでなんとなくしかわからないが、思ったよりだいぶ読めるなという感覚。

## Step3

### 3回連続で再現できるようになる

他で呼び出されても大丈夫なように値のチェックを多めに書いてみた。

indexは負でアクセスされても良いように、最大値を超えても良いようにしてみた。

indexが負で絶対値がタプルの長さより大きいときは、範囲外アクセスをしてしまうが、その時はおかしな使い方されている気がするので、assertにした。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        return self.get_max_money(tuple(nums))
        
    @cache
    def get_max_money(self, nums: Tuple[int], index: int=-1) -> int:
        if not nums:
            return 0
        length = len(nums)
        if index < 0:
            index = length + index
            assert index >= 0
        if index >= length:
            index = length - 1

        if index == 0:
            return nums[0]
        if index == 1:
            return max(nums[0], nums[1])
        candidate = self.get_max_money(nums, index-2) + nums[index]
        return max(candidate, self.get_max_money(nums, index-1))
```
