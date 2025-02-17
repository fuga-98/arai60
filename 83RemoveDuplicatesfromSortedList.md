# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照する。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        current = head
        # 現在のノードとその次のノードが存在する限りループする
        while current and current.next:
            if current.val == current.next.val:
                # 重複しているので、次のノードをスキップする
                current.next = current.next.next
            else:
                # 重複していないので、次のノードへ進む
                current = current.next
        return head
```

現在のノードと次のノードと一致したらスキップすればよいという発想はできた。

何をReturnすればよいのかが分からなくなった。

入出力はあっているみたいだが、returnしているのがheadなのがもやもやする。

## Step2

### 同じ問題を解いた人のプルリクを見る

### 解答

- while文を分割する

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        done_head = head
        while node:
            while node.next and node.next.val == node.val:
                node.next = node.next.next
            node = node.next
        return done_head
```

- IfとContinueで書く

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        while node:
            if node.next and node.next.val == node.val:
                node.next = node.next.next
                continue
            node = node.next
        return head
```

- 再帰で書く

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 終了条件
        if head is None or head.next is None:
            return head
        if head.val == head.next.val:
            return self.deleteDuplicates(head.next)
        else:
            head.next = self.deleteDuplicates(head.next)
            return head
```

### メモ

再帰の考え方

https://discord.com/channels/1084280443945353267/1261259843671687188/1262997002891821056

何を受け取って何を返せば問題が解けるか、を考えると理解できた。

## 気になった点

- メソッドに副作用があることが気になる。

→Deepcopyを使えば副作用がなくなるようだ

https://docs.python.org/ja/3/library/copy.html

<aside>
深いコピー操作には、しばしば浅いコピー操作の時には存在しない 2 つの問題がついてまわります:

- 再帰的なオブジェクト (直接、間接に関わらず、自分自身に対する参照を持つ複合オブジェクト) は再帰ループを引き起こします。

[`deepcopy()`](https://docs.python.org/ja/3/library/copy.html#copy.deepcopy) 関数では、これらの問題を以下のようにして回避しています:

- 現時点でのコピー過程ですでにコピーされたオブジェクトの `memo` 辞書を保持する。
</aside>

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None
        new_head = deepcopy(head)
        node = new_head
        while node:
              if node.next and node.next.val == node.val:
                node.next = node.next.next
                continue
            node = node.next
        return new_head
        
```

## Step3

### 3回連続で再現できるようになる

2重ループとIfで書いた。

2重ループのほうが直感的な感じがする
```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        while node:
            if node.next and node.next.val == node.val:
                node.next = node.next.next
                continue
            node = node.next
        return head
```

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        while node:
            while node.next and node.next.val == node.val:
                node.next = node.next.next
            node = node.next
        return head       
```
