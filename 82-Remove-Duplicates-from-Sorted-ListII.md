# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照する。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

再帰だと難しそうだ。

headを決めたい。番兵みたいなものを使うのかな。

3つ見れば判断できそう。

→判断はできても、うまくいったら何をすればよいかわからない

**↓あきらめたコード**

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        result = None
        previous = None
    
        while node:
            current = node.val
            if node.next is None:
                next = None
            else:
                next = node.next.val

            if previous != current and current != current.next:
                # 合格
                if result is None:
                    result = node
                else:
                    result.next = node
            else:
                previous = node.val
                # 不合格
                node = node.next
```

解答のコード

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # headが重複するnodeの場合
        # 重複しない要素が先頭になるまで進める
        while head and head.next and head.val == head.next.val:
            while head.next and head.val == head.next.val:
                head = head.next
            head = head.next

        # ここからの実装はheadが重複しない要素なので
        # dummy_headのようにheadを使うことが出来る
        # ただしheadがNoneのときがあるので注意
        if not head:
            return head
        unique = head
        node = head.next
        while node and node.next:
            if node.val == node.next.val:
                while node.next and node.val == node.next.val:
                    node.next = node.next.next
                unique.next = node.next
            else:
                unique.next = node
                unique = node

            node = node.next
        return head

```

## Step2

### 見ただけだと理解できないので再現する

自分で実装するとこうなる

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(None)
        result = dummy
        node = head
        while node:
            if node.next is None or node.val != node.next.val:
                result.next = ListNode(node.val)
                result = result.next
            else:
                while node.next and node.val == node.next.val:
                    node.next = node.next.next

            node = node.next
        return dummy.next   
```

### 同じ問題を解いた人のプルリクを見る

- continueを使うと読む側として楽

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        dummy = ListNode(None)
        result = dummy
        node = head
        while node:
            if node.next and node.val == node.next.val:
                while node.next and node.val == node.next.val:
                    node.next = node.next.next
                node = node.next
                continue
            else:
                result.next = ListNode(node.val)
                result = result.next
                node = node.next
                continue
        return dummy.next
```

- Stackを使った実装

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        stack = []
        node = head
        while node:
            if not stack or stack[-1] != node.val:
                stack.append(node.val)
                node = node.next
            else:
                value = stack[-1]
                stack.pop()
                while node and value == node.val:
                    node = node.next
        
        prev = None
        result = None
        while stack:
            result = ListNode(stack.pop(),prev)
            prev = result
        return result
```

- 再帰を使った実装
    - https://discord.com/channels/1084280443945353267/1195700948786491403/1197102971977211966

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        if head.val != head.next.val:
            head.next = self.deleteDuplicates(head.next)
            return head
        else:
            while head.next and head.val == head.next.val:
                head.next = head.next.next
            return self.deleteDuplicates(head.next)
```

これが一番わかりやすい

## Step3

### 3回連続で再現できるようになる

再帰で書いた。
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        if head.val != head.next.val:
            head.next = self.deleteDuplicates(head.next)
            return head
        else:
            while head.next and head.val == head.next.val:
                head.next = head.next.next
            return self.deleteDuplicates(head.next)   
```
