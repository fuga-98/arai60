# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        # 一個ずつ確かめるはできそう
        # hashmapに入れて一個ずつ見ればよいか。
        char_to_count = defaultdict(int)
        for char in s:
            char_to_count[char] += 1
            
        for i, char in enumerate(s):
            if char_to_count[char] == 1:
                return i
        
        return -1
```

Counterでもかける

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_count = Counter(s)
        
        for i, char in enumerate(s):
            if char_to_count[char] == 1:
                return i
        
        return -1
```

## Step2

### 同じ問題を解いた人のプルリクを見る

- Dictionaryでも解ける
- 一回のループでも書ける

- 頭の体操を兼ねて再帰で書く(len(s)は10*5なので1000を超えるためNG）

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        # 再帰で
        def first_uniq_char(s, length):
            if not s:
                unique_stack = []
                char_to_index = {}
                return char_to_index, unique_stack

            char = s[0]
            char_to_index, unique_stack = first_uniq_char(s[1:], length)
            
            if char not in char_to_index:
                char_to_index[char] = length - len(s)
                unique_stack.append(length - len(s))
                return char_to_index, unique_stack
                
            if char_to_index[char] is None:
                return char_to_index, unique_stack

            unique_stack.remove(char_to_index[char])
            char_to_index[char] = None
            return char_to_index, unique_stack

        _, unique_stack = first_uniq_char(s, len(s))

        if not unique_stack:
            return -1
        return unique_stack.pop()
```

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_count = Counter(s)
        for i, char in enumerate(s):
            if char_to_count[char] == 1:
                return i
        
        return -1
```

