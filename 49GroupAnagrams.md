# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

- stringをsortするとListになることを知らなかった
- さすがにもっと簡単に書けそう

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        # 文字の種類と数が一致すればアナグラム
        # ハッシュマップかソート
        elements_to_word = {}
        for word in strs:
            characters = sorted(word)
            elements = ''
            for character in characters:
                elements += character
            if elements in elements_to_word:
                elements_to_word[elements].append(word)
                continue
            elements_to_word[elements] = [word]
        return [words for words in elements_to_word.values()]
```

## Step2

### 同じ問題を解いた人のプルリクを見る

- https://github.com/Fuminiton/LeetCode/pull/12/files#diff-62f09ec86d7a36279b7f76c377e2837308a6a9d6caed621efc4432c7098f620c

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        # sort 計算量は　O(NlogN)
        # それを配列の要素の数だけ行うから　100*7 * 10^4 = 10^6 くらいなので大丈夫そう
        elements_to_word = defaultdict(list)
        for word in strs:
            element = str(sorted(word))#''.join(sorted(word))
            print(element)
            elements_to_word[element].append(word)
        return list(elements_to_word.values())
```

- str(list)と’’.join(list)はまったく違うもの

```python
my_list = ['a', 'b', 'c']
print(str(my_list))
# 出力: "['a', 'b', 'c']"
```

```python
my_list = ['a', 'b', 'c']
print(''.join(my_list))
# 出力: "abc"
```

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        
        def get_char_count(word):
            NUM_ALPHABET = 26
            char_count = [0] * NUM_ALPHABET 
            for char in word:
                count = ord(char) - ord('a')
                if count< 0 or NUM_ALPHABET < count:
                    raise ValueError('文字列に不正な文字が含まれています') 
                char_count[count] += 1
            return tuple(char_count)

        count_to_word= defaultdict(list)
        for word in strs:
            count = get_char_count(word)
            count_to_word[count].append(word)
        return list(count_to_word.values())

```

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        elements_to_word = defaultdict(list)

        for word in strs:
            elements = str(sorted(word))
            elements_to_word[elements].append(word)
        return list(elements_to_word.values()) 
```

