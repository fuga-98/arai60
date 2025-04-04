# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

### 思考ログ

類題は解いたことがあるが、繋ぎ変えを実装していなかったので練習する。

時間計算量はO(n),空間計算量はO(1)

500 / 1000k = 0.5msくらい

繋ぎ変えてやる方法がうまく書けなかった。

手作業でやってみたら理解できた。

```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        if head is None:
            return None
        dummy_head = ListNode(next=head)
        node = dummy_head
        for _ in range(left - 1):
            node = node.next
        mark_head = node
        tail = node.next
        assert tail is not None
        for _ in range(right - left):
            node = tail.next
            tail.next = node.next
            node.next = mark_head.next
            mark_head.next = node
        return dummy_head.next
```

## Step2

### 同じ問題を解いた人のプルリクを見る

https://github.com/Ryotaro25/leetcode_first60/pull/65/files

- 一度List(dictionary)にしてから構築するやり方
- 空間計算量はO(n)かかるが割とシビアになる必要はないらしい。

https://github.com/irohafternoon/LeetCode/pull/7

- 繋ぎ変えが三つあるらしい
    
    https://discord.com/channels/1084280443945353267/1355246975309844550/1355246980200399062
    
    ```python
    繋ぎ変える方法は、実は3種類はあるようです。
    「リンク」を中心に見ていて、範囲内のすべてのリンクを順番にひっくり返すという方法
    先頭の前にダミーをつけて、先頭の次のノードをダミーの後ろに挿入していく方法
    ひっくりかえす前の鎖と後の鎖を用意して、前のやつの先頭を後のやつの先頭につけていく方法
    ```
    
    理解できた。
    
    自分が最初に書いたのは2のダミーを付けるやり方か。
    
    この繋ぎ変えの方法が複数あることに気づけるようになりたい。
    

```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        def skip_nodes(node, n):
            for _ in range(n):
                if not node:
                    return None
                node = node.next
            return node
       
        def reverse_segment(node, count):
            reversed_head = None
            for _ in range(count):
                if not node:
                    return reversed_head, node
                next_node = node.next
                node.next = reversed_head
                reversed_head = node
                node = next_node
            return reversed_head, node

        if not head or left == right:
            return head
        dummy = ListNode(0)
        dummy.next = head
       
        last_node_before_reverse = skip_nodes(dummy, left - 1)
        if not last_node_before_reverse or not last_node_before_reverse.next:
            return dummy.next
        head_of_reverse = last_node_before_reverse.next
        reversed_head, next_node = reverse_segment(head_of_reverse, right - left + 1)
        last_node_before_reverse.next = reversed_head
        head_of_reverse.next = next_node

        return dummy.next
```

## Step3

### 3回連続で再現できるようになる

range(0)だとループがないの知らなかった。

短い変数名が好みだったが、長いほうが何かを忘れたときに思い出しやすいと感じた。

自分にとって難しいと感じるものは長めにつけておくほうが良いかもしれない。

自力で解くことにこだわって時間を浪費してしまう癖をなくしたい。

```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        def skip_nodes(node, num):
            for _ in range(num):
                node = node.next
            return node
        
        def reverse_nodes(node, num):
            reversed_head = None
            for _ in range(num):
                print(node.val)
                next_node = node.next
                node.next = reversed_head
                reversed_head = node
                node = next_node
            return reversed_head, node

        if head is None or left == right:
            return head
        dummy = ListNode()
        dummy.next = head
        node = dummy
        last_node_before_reverse = skip_nodes(dummy, left - 1)
        reversed_tail = last_node_before_reverse.next
        reversed_head,  next_node = reverse_nodes(reversed_tail, right - left + 1)
        last_node_before_reverse.next = reversed_head
        reversed_tail.next = next_node
        return dummy.next
```

```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        def reverse_segment(last_node_before_reversed, count):
            if last_node_before_reversed is None or last_node_before_reversed.next is None:
                return
            reversed_tail = last_node_before_reversed.next
            for _ in range(count):
                node = reversed_tail.next
                if node is None:
                    return
                reversed_tail.next = node.next
                node.next = last_node_before_reversed.next
                last_node_before_reversed.next = node

        if head is None or left == right:
            return head
        dummy = ListNode()
        dummy.next = head
        node = dummy
        for _ in range(left - 1):
            node = node.next
        last_node_before_reversed = node
        reverse_segment(last_node_before_reversed, right - left)
        return dummy.next

```

```python
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        def reverse_for_count(last_node_before_reversed, count):
            if last_node_before_reversed is None or last_node_before_reversed.next is None:
                return last_node_before_reversed
            reversed_tail = last_node_before_reversed.next
            for _ in range(count):
                node = reversed_tail.next
                if node is None:
                    return last_node_before_reversed
                reversed_tail.next = node.next
                node.next = last_node_before_reversed.next
                last_node_before_reversed.next = node
            return last_node_before_reversed.next
        
        if head is None or left == right:
            return head
        dummy = ListNode()
        dummy.next = head
        node = dummy
        for _ in range(left - 1):
            node = node.next
        last_node_before_reversed = node
        last_node_before_reversed.next = reverse_for_count(last_node_before_reversed, right - left)
        return dummy.next
```

