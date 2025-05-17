# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

総当たりだとO(N**2)

spaceが不安。

出現位置を持っておけばよいかな。

具体例を用いて確認してみる。

abbcd

1, 2,1,2,3

abfbcd

1,2,3,2,3,4

動的計画法っぽくいけそう。

時間空間ともにO(N)でいけそう。

実行時間は5/10**4 / 10**6 = 5msくらい

そういえばLeetcodeのLTEってどれくらいなんだろう

---

GPT作

### 言語別緩和係数 (経験則)

LeetCode 公式は数値を公開していませんが、提出結果のヒストグラムや判定ロジックの解析から、次の程度の倍率が使われていると言われています。

| 言語 | 目安の上限 |
| --- | --- |
| C/C++ | 1–2 s |
| Java / Kotlin | 2–4 s |
| Python (CPython 3) | 8–10 s |
| JavaScript / TypeScript (Node) | 6–8 s |
| Go / Rust / Swift | 2–3 s |

> Reddit の実体験でも「C++ 2 秒 / Python 10 秒くらい」という証言が多数。Reddit
> 

---

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if not s:
            return 0
        c_to_last = {}
        prefix = [1] * len(s)
        c_to_last[s[0]] = 0
        for i in range(1, len(s)):
            if s[i] not in c_to_last:
                prefix[i] = prefix[i-1] + 1
                c_to_last[s[i]] = i
                continue
            prefix[i] = prefix[i-1] - prefix[c_to_last[s[i]]] + 1
            c_to_last[s[i]] = i
        print(prefix)
        return max(prefix)
```

"tmmzuxt”でWA　うーんわからん。

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if not s:
            return 0
        c_to_last = {}
        prefix = [1] * len(s)
        c_to_last[s[0]] = 0
        last_broken = -1
        for i in range(1, len(s)):
            print(last_broken)
            c = s[i]
            if c not in c_to_last:
                prefix[i] = prefix[i-1] + 1
                c_to_last[c] = i
                continue
            if c_to_last[c] >= last_broken:
                prefix[i] = prefix[i-1] - prefix[c_to_last[c]] + 1
                last_broken = c_to_last[c]
                c_to_last[c] = i
                continue
            prefix[i] = prefix[i-1] + 1
            c_to_last[c] = i
        print(prefix)
        return max(prefix)
```

"bpfbhmipx”でWA。prefix[i] = prefix[i-1] - prefix[c_to_last[c]] + 1が間違っていそうだが、なんかこれで試行錯誤しても意味ない気がする。

わからないので解答見る。

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if not s:
            return 0
        c_to_last = {}
        prefix = [1] * len(s)
        c_to_last[s[0]] = 0
        last_broken = 0
        for i in range(1, len(s)):
            c = s[i]
            if c_to_last.get(c, -1) >= last_broken:
                prefix[i] = prefix[i-1] - prefix[c_to_last[c]] + 1
                last_broken = c_to_last[c]
            else:
                prefix[i] = prefix[i-1] + 1
            c_to_last[c] = i
        return max(prefix)
```

ソースをきれいにしてみた。

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/olsen-blue/Arai60/pull/49/files

- Sliding Window = 尺取り法　らしい。尺取り虫のイメージとのこと。
- 私のstep1のソースは左側をうまく縮められていなそうでした。今見ると動的計画法を使う意味ないですね。
- left = max(left, char_to_last_index.get(s[right], -1) + 1)の＋１書き忘れました。

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        left = 0
        max_length = 0
        char_to_last_index = {}
        for right in range(len(s)):
            left = max(left, char_to_last_index.get(s[right], -1) + 1)
            max_length = max(max_length, right - left + 1)
            char_to_last_index[s[right]] = right
            print(max_length)
        
        return max_length
```

https://github.com/philip82148/leetcode-swejp/pull/3

- 文字コードの話
- Dictionaryではなく配列を使うとこんな感じになるのか。
- 速度は多少早くなるんだろうが、拡張性を失うのは嫌だなという気持ちになりました。

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        start = 0
        char_to_last_index = {}
        max_length = 0
        for i, c in enumerate(s):
            start = max(start, char_to_last_index.get(c, -1) + 1)
            max_length = max(max_length, i - start + 1)
            char_to_last_index[c] = i
        return max_length
```

+1が複数あるのでイメージしながら書かないとミスりますね
