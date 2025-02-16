## Step1

分からなかったので答えを見た。

LeetCodeが初めてだったため、入力の仕方と問題文の読解でつまずいていた。

```
class Solution:
    def hasCycle(self, head: Optional[ListNode]):
        reached = set()
        while head:
            if head in reached:
                return True
            reached.add(head)
            head = head.next
        return False
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/katsukii/leetcode/pull/12

https://github.com/plushn/SWE-Arai60/pull/1

https://github.com/quinn-sasha/leetcode/pull/1

https://github.com/Fuminiton/LeetCode/pull/1

https://github.com/mura0086/arai60/pull/5

### 気になったコメント

- headが動いていくのはわかりずらい。nodeにするべき
- currentという変数名は情報が少ないので、意図なしで使うべきではない
- 変数名に型名を入れるのは良くない
- 別の解法があるみたいだが、知らなくても良いらしい
    - https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.2k4z0wt6ytf9

### 解答

```
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        node = head
        while node:
            if node in visited:
                return True
            visited.add(node)
            node = node.next
        return False
```

## Step3

### 3回連続で再現できるようになる

```
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        node = head
        while node:
            if node in visited:
                return True
            visited.add(node)
            node = node.next
        return False
```

### ドキュメントを読む

Pythonの命名規則

- https://pep8-ja.readthedocs.io/ja/latest/#
    - 関数は小文字、インデントはスペース４つ

Set

- ドキュメント(https://docs.python.org/ja/3.13/library/stdtypes.html#set)
    - setの要素はハッシュ可能でなくてはいけない
    - setハッシュ値を持たない。
    - setはsetの要素にならない。
- 実装(https://github.com/python/cpython/blob/main/Objects/setobject.c)

### 簡単なSetの実装をしてみる。

衝突、配列のリサイズ、エラーハンドリングは実装しない。

```
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = SimpleSet()
        node = head
        while node:
            if visited.search(node):
                return True
            visited.add(node)
            node = node.next
        return False


class SimpleSet:

    def __init__(self,capacity=16):
        self.capacity = capacity
        self.hash_list = [None] * capacity

    def _hash(self,x) -> int:
        return hash(x) % self.capacity

    def search(self,x) -> bool:
        index = self._hash(x)
        return self.hash_list[index] == x

    def add(self,x)　-> None:
        index = self._hash(x)
        self.hash_list[index] = x

```

なぜsetの挿入や検索がO(1)なのか理解できた
