# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照する。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

141とReturnするものが違うだけなのでそのまま書いた。

return Nullと書いてしまった。

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()
        while node:
            if node in visited:
                return node
            visited.add(node)
            node = node.next
        return None
```

NoneとNullの違いを調べる

## Step2

### 同じ問題を解いた人のプルリクを見る

### コードの整え方

- https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.9kpbwslvv3yv

### 解答

while 1を使ったバージョン

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()
        while 1:
            if node is None:
                return None
            if node in visited:
                return node
            visited.add(node)
            node = node.next
```

分かりにくいが、こうも書ける

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()
        while 1:
            if node is None or node in visited:
                return node
            visited.add(node)
            node = node.next
```

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()
        while 1:
            if node is None or node in visited:
                return node
            visited.add(node)
            node = node.next
```

### ドキュメントを読む

Noneについて

https://docs.python.org/ja/3.10/c-api/none.html

→Noneはオブジェクト
