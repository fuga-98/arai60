# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

動きません。

けっこう時間を費やしてしまったので、次回以降は解法がわからなければ解答をすぐ見る

```python
class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        # まず入出力を合わせる
        num_c = len(beginWord)

        def is_one_diff(x, y):
            diff_count = 0
            for i in range(num_c):
                if x[i] == y[i]:
                    continue
                diff_count += 1
                if diff_count > 1:
                    return False
            return diff_count == 1

        def calculate_min_length(target, used=set()):
            if is_one_diff(target, endWord):
                return 1
            word_to_min = {}
            for i, word in enumerate(wordList):
                if word in used:
                    continue
                if not is_one_diff(target, word):
                    continue
                used.add(word)
                length =  calculate_min_length(word, used) + 1
                if word not in word_to_min:
                    word_to_min[word] = length
                else:
                    word_to_min[word] = min(word_to_min[word], length)
            
            return min(word_to_min.values())

        return calculate_min_length(beginWord)                 

```

最初スタックで書いてしまった。bfsが分かった気がする

```python
class Solution:
    def ladderLength(self, begin_word: str, end_word: str, word_list: List[str]) -> int:
        # 計算量は文字数×単語数の階乗
        # 多すぎるきがする
        num_c = len(begin_word)

        def is_one_diff(x, y):
            diff_count = 0
            for i in range(num_c):
                if x[i] == y[i]:
                    continue
                diff_count += 1
                if diff_count > 1:
                    return False
            return diff_count == 1

        used = set()
        used.add(begin_word)
        queue = deque([(1, begin_word)])
        

        while queue:
            distance, base = queue.popleft()

            for word in word_list:
                if word in used:
                    continue
                if not is_one_diff(base, word):
                    continue
                if word == end_word:
                    return distance + 1
                used.add(word)
                queue.append((distance + 1, word))
            
        return 0
        
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/SuperHotDogCat/coding-interview/pull/45/files

- 時間計算量を考える
    - https://github.com/SuperHotDogCat/coding-interview/pull/45/files#r1980741221

https://github.com/tshimosake/arai60/pull/11

- string、文字コードについて
    - https://docs.python.org/ja/3.13/library/string.html
    - https://github.com/t0hsumi/leetcode/pull/12#discussion_r1886759238

https://github.com/t0hsumi/leetcode/pull/20

- 見積もり、このくらい精度で見積もりたい
    - https://github.com/t0hsumi/leetcode/pull/20/files#r1944383585
- 入出力に引っ張られないようにする
    - https://github.com/t0hsumi/leetcode/pull/20/files#r1938166243
- ジェネレーター式
    - 最大でword_listの分だけメモリを減らせる
    - 今回はそこまで気にしなくてもよいと思っている。set(word_list)を使うので、メモリの消費量は半分程度くらいにしかならない。
    - https://docs.python.org/ja/3.11/reference/expressions.html#generator-expressions

https://github.com/olsen-blue/Arai60/pull/20

- 分かりやすい
- wordListを消していけばよいのか

### 書き直す

関数に詰め込みすぎている感じがある

```python
class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        num_word = len(beginWord)
        word_queue = deque([(beginWord, 1)])
        word_set = set(wordList)

        def search_end_word(word: str, step: int) -> Optional[int]:
            for i in range(num_word):
                for c in string.ascii_lowercase:
                    target = f'{word[:i]}{c}{word[i+1:]}'
                    if target not in word_set:
                        continue
                    if target == endWord:
                        return step + 1
                    word_queue.append((target, step + 1))
                    word_set.remove(target)
            return None
        
        while word_queue:
            word, step = word_queue.popleft()
            result = search_end_word(word, step)
            if result is not None:
                return result
        
        return 0
```

## Step3

### 3回連続で再現できるようになる

13分

18分　wordsとwordを書き間違えた。スコープが長いときは使わないほうが良いかも 

7分

```python
class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        usable = set(wordList)

        def get_next_words(word):
            next_words = []
            for i in range(len(word)):
                for c in string.ascii_lowercase:
                    candidate = f'{word[:i]}{c}{word[i+1:]}'
                    if candidate not in usable:
                        continue
                    next_words.append(candidate)
                    usable.remove(candidate)
            return next_words

        queue = deque([(beginWord, 1)])
        while queue:
            word, step = queue.popleft()
            next_words = get_next_words(word)
            next_step = step + 1
            for target in next_words:
                if target == endWord:
                    return next_step
                queue.append((target, next_step))
        return 0

```
