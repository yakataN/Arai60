##### 002 add_two_numbers
- [x] 2025/05/13

- ListNodeのポインタで渡されたリストをreverse→joinして、下の位から並べたListNodeにすることを要求されている。
- ListNode→int→ListNodeが要求。ListNode→intはいくつかあり、
    - listNodeを最初からみて、足し算（iter 進む事に10かけていく)
        - ex. 2→４→３は2x1+4x10+3x10^2
    - stringとして処理してreverse(or reverseしてString)→intとして計算
- が考えつく
- →Stringを経由するほうが楽そうだし、可読性も上がりそう。
- →簡単のためにそのままJoinして、StringにしてReverseしてバラしてIntに変換
- 調べてもListNode出て来ないと思ったらコメントでstructが乗っていた。

```python
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        ary1 = []
        ary2 = []
        iter1 = l1
        iter2 = l2
        while iter1 is not None:
            ary1.append(iter1.val)
            iter1 = iter1.next
        
        while iter2 is not None:
            ary2.append(iter2.val)
            iter2 = iter2.next

        reverse_num1 = int("".join(map(str, reversed(ary1))))
        reverse_num2 = int("".join(map(str, reversed(ary2))))
        sum_num = reverse_num1 + reverse_num2

        # 上の桁から処理してlistNodeをつくる
        ans = None
        for i in map(lambda x: int(x), list(str(sum_num))): 
            ans = ListNode(val=i, next = ans)

        return ans
```

Step1だが、最後に変数iを使ったこと以外はstep2できているように見える。
型変換のmap はそのままラムダ使わなくても行けるのを忘れていた。
以上変えてstep2
```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        ary1 = []
        ary2 = []
        iter1 = l1
        iter2 = l2
        while iter1 is not None:
            ary1.append(iter1.val)
            iter1 = iter1.next
        
        while iter2 is not None:
            ary2.append(iter2.val)
            iter2 = iter2.next

        reverse_num1 = int("".join(map(str, reversed(ary1))))
        reverse_num2 = int("".join(map(str, reversed(ary2))))
        sum_num = reverse_num1 + reverse_num2

        # 上の桁から処理してlistNodeをつくる
        ans = None
        for val in map(int, list(str(sum_num))): 
            ans = ListNode(val, next = ans)

        return ans
```

step3
```python
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
#  23:28 start
        ary1 = []
        while l1 is not None:
            ary1.append(l1.val)
            l1 = l1.next

        ary2 = []
        while l2 is not None:
            ary2.append(l2.val)
            l2 = l2.next

        reverse_num1 = int(''.join(list(reversed(list(map(str, ary1))))))
        reverse_num2 = int(''.join(list(reversed(list(map(str, ary2))))))

        sum_num = reverse_num1 + reverse_num2

        ans = None
        for val in map(int, list(str(sum_num))):
            ans = ListNode(val, next=ans)

        return ans
# 23:34 end
# 6 min
```

反省
未知のStruct, Classの扱い方がわかるまでが遅い。（連結リストは知っているが、実装となると手が止まる）
step2と3で方針同じだがコードが異なっているのはいいのか。
