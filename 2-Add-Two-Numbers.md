# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照する。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

3から見ていただけると助かります

# 実践

## Step1

### 思考ログ

素直に数値に直してから足して、数値からNodeに戻す

除算の仕様を知らなかった。

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:

        def convertToNode(num: int) -> Optional[ListNode]:
            if num < 1:
                return None
            # 0に小数点がはいってしまう
            # next_num = num / 10    
            next_num = num // 10    
            next_node = convertToNode(next_num)
            return ListNode(num % 10,next_node)

        # 数値に変えて足して、Nodeに戻せばよさそう
        sum = 0
        node1 = l1
        order1 = 1
        while node1:
            sum = sum + node1.val * order1
            order1 = order1 * 10
            node1 = node1.next
        node2 = l2
        order2 = 1
        while node2:
            sum = sum + node2.val * order2
            order2 = order2 * 10
            node2 = node2.next
        print(sum)
        result = convertToNode(sum)
        if result is None:
            return ListNode(0)
        return result
```

## Step2

### 解きなおす

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:

        def convertNodeToInt(head: Optional[ListNode], order: int = 1) -> int:
            value =  head.val * order
            if head.next is None:
                return value
            return value + convertNodeToInt(head.next,order * 10)      

        def convertIntToNode(num: int) -> Optional[ListNode]:
            if num < 1:
                return None
            next_num = num // 10    
            next_node = convertIntToNode(next_num)
            return ListNode(num % 10,next_node)
            
        sum = convertNodeToInt(l1) + convertNodeToInt(l2)
        result = convertIntToNode(sum)
        if result is None:
            return ListNode(0)
        return result
```

### 同じ問題を解いた人のプルリクを見る

同じ解き方をした人があまりいなくて驚いた。

### 定番の解き方

それぞれ足していく解き方は分岐が大変そうだという認識で避けた

書けたが、やはり分岐で何度かエラーになった

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        node1 = l1
        node2 = l2
        result = ListNode(None)
        head = result # return用
        carry = 0 # 繰り上がり
        while node1 or node2 or carry == 1:
            value1, value2 = 0, 0
            if node1:
                value1 = node1.val
            if node2:
                value2 = node2.val 

            sum = value1 + value2 + carry
            if sum <= 9:
                carry = 0
                result.next = ListNode(sum)
                result = result.next
            else:
                # 繰り上がり
                carry = 1
                result.next = ListNode(sum - 10)
                result = result.next
            # Noneなら動かさない
            if node1:
                node1 = node1.next
            if node2:
                node2 = node2.next
        return head.next
```

### 再帰の解き方

何回かエラーにはなったが、見返さずに解けた

番兵を置くの楽だ。

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode], carry :int=0) -> Optional[ListNode]:
        if l1 is None and l2 is None and carry == 0:
            return None
        if l1 is None:
            l1 = ListNode(0)
        if l2 is None:
            l2 = ListNode(0)
        sum = l1.val + l2.val + carry
        if sum <= 9:
            carry = 0
            head = ListNode(sum)
            head.next = self.addTwoNumbers(l1.next, l2.next, carry)
            return head
        else:
            carry = 1
            head = ListNode(sum-10)
            head.next = self.addTwoNumbers(l1.next,l2.next,carry)
            return head
```

## Step3

### 3回連続で再現できるようになる

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode], carry :int=0) -> Optional[ListNode]:
        # 終了条件
        if l1 is None and l2 is None and carry == 0:
            return None
        # 番兵
        if l1 is None:
            l1 = ListNode(0)
        if l2 is None:
            l2 = ListNode(0)
        sum = l1.val + l2.val + carry
        # 一桁目がNode.val 2桁目がcarry
        carry,value = divmod(sum,10)
        head = ListNode(value)
        head.next = self.addTwoNumbers(l1.next,l2.next,carry)
        return head
                    
```
