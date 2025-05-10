# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照する。ドキュメントを参照する。

Step3 : 3回続けてエラーが出ないように書く。

# 実践

## Step1

### 思考ログ

```python
class Solution:
    def isValid(self, s: str) -> bool:
        # Stack使えばいけそう
        open_brackets = ('(','{','[')
        close_brackets = (')', '}', ']')
        brackets_pair = {'(': ')','{': '}', '[': ']'}
        opening = []
        for char in s:
            if char in open_brackets:
                # Stackに積むものの考慮不足した
                opening.append(brackets_pair[char])
                continue
            if char in close_brackets:
                # 空のStack考慮不足した
                if len(opening) == 0 or  char != opening.pop():
                    return False
                continue
        # 開きが残っていたらFalse
        if len(opening) != 0:
            return False
        return True
                   
```

比較対象を間違えることが多い

node.val == [node.next](http://node.next).val　と書くところを node == node.next と書きがち

## Step2

### 同じ問題を解いた人のプルリクを見る

- if not opening_stackやreturn len(opening_stack) == 0
- LeetCodeはキーワード引数で呼び出しているわけではないので変更できる
- ({[ の場合を先に処理して continue にしてインデント下げたほうが読みやすいでしょう。
- ボトルネックでないところを最適化しない

```python
class Solution:
    def isValid(self, brackets: str) -> bool:
        # Stack使えばいけそう
        open_brackets = '({['
        opent_to_close = {'(': ')','{': '}', '[': ']'}
        opening = []
        for bracket in brackets:
            if bracket in open_brackets:
                opening.append(opent_to_close[bracket])
                continue
            if len(opening) == 0 or  bracket != opening.pop():
                return False
            continue
        return len(opening) == 0
```

変数名の感覚はついてきた感じがする

### ドキュメントを読む

https://docs.python.org/ja/3.13/library/stdtypes.html#text-sequence-type-str

Stringはシーケンス型の演算をすべて持っている

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def isValid(self, brackets: str) -> bool:
        opening = []
        OPEN_BRACKETS = '({['
        OPEN_TO_CLOSE = {'(':')','{':'}','[':']'}
        for target in brackets:
            if target in OPEN_BRACKETS:
                opening.append(OPEN_TO_CLOSE[target])
                continue
            if not opening or target != opening.pop():
                return False
            continue
        return not opening
```

