# **206. Reverse Linked List**


# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照する。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 再帰、スタックで解けそう
        # スタックで解く
        node = head
        node_stack = []
        result = ListNode(None)
        pointer = result
        while node:
            node_stack.append(node.val)
            node = node.next
        while node_stack:
            result.next = ListNode(node_stack.pop())
            result = result.next
        return pointer.next
```

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 再帰
        if head is None:
            return None
        if head.next is None:
            return head
        reverse = self.reverseList(head.next)
        final = reverse
        while final.next:
            final = final.next
        final.next = ListNode(head.val)
        return reverse
```

- 見ないで書けたがreverse.next = ListNode(head.val)と最初書いてしまった。
- Returnするものを変えるとエラー→ヘルパーメソッド作成すればよい

## Step2

### 同じ問題を解いた人のプルリクを見る

- これは繋ぎ変えを見たいのではないか
- はじめのループで .next をどのように引き継ぐかを意識してコードを書きましょう。

繋ぎ変えて書いてみる。20分くらいかかったが書けた。

新しいオブジェクトを作ると書きやすいが、何か考慮すべき事項はあるのだろうか。

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None
        node = head
        prev = None
        while node:
            # 新しいオブジェクトを作る
            result = ListNode(node.val)
            result.next = prev
            prev = result
            node = node.next
        return result
```

もう一度再帰で解く

```python
class Solution:
    def reverseListHelper(self, head: Optional[ListNode]) ->Tuple[Optional[ListNode],Optional[ListNode]]:
     # 再帰
        if head is None:
            return None,None
        if head.next is None:
            return head,head
        
        reverse,tail = self.reverseListHelper(head.next)
        tail.next = ListNode(head.val)
        return reverse, tail.next

    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reverse, _ = self.reverseListHelper(head)
        return reverse
```

## Step3

### 3回連続で再現できるようになる

Node付け替えと再帰で書いた

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        reverse = None
        prev = None
        while node:
            reverse = ListNode(node.val)
            reverse.next = prev
            prev = reverse
            node = node.next
        return reverse
```

```python
class Solution:
    def reverseListHelper(self, head: Optional[ListNode]) -> Tuple[Optional[ListNode],Optional[ListNode]]:
        if head is None:
            return None,None
        if head.next is None:
            return head, head
        reverse, tail = self.reverseListHelper(head.next)
        tail.next = ListNode(head.val)
        return reverse, tail.next

    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reverse, _ = self.reverseListHelper(head)
        return reverse
```

