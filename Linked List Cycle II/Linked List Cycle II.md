### Linked List Cycle II_leetcode

#### step1

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # ListNode -> indexとするdictをつくれればいけるのだが、こういう複雑な構造もkeyになれる、つまりHash化できるのかがわからない（多分できない）
        # ２回ループになるが、１回目でループした時点でのListNodeを得て、２回目で先頭から再度追い直して、何番目であったかをみる。
        if head is None:
            return None

        seen_nodes = set()
        seen_node = head
        is_cycled = True
        while True:
            if seen_node is None:
                is_cycled = False
                break
            if seen_node in seen_nodes:
                ans_node = seen_node
                break
            seen_nodes.add(seen_node)
            seen_node = seen_node.next
            
        if not is_cycled:
            return None

        return ans_node

        # posを答えるものだと勘違いしてnodeを取ってきてからpos(iterの回した回数）を取っていた跡地
        # while True:
        #     if head == ans_node:
        #         return ans_index
        #     head = head.next
        #     ans_index += 1

```
8minくらい

- 最初pos(index)を返すものと勘違いしてごちゃごちゃしてしまった。> the `head` of a linked list, return _the node where the cycle begins_) と明記してあったが、見逃している。返り値の型も見逃していて2敗。そもそもの英語力が足りていない話もある。
- seen_nodeは単にnodeで良さそう
- `while node is not None:`でループを回したい（無限ループはどこで抜けるか見るのに認知負荷が高いため）。is_cycledが使えなくなるが、ループ抜けた時に`node is None`かどうかで判断すれば良さそう。
- そうすると最初の`if head is None:`は省略できる
- 最後の判定は`return node`でいい（ループないならばNoneが返されるため）

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        seen_nodes = set()
        node = head
        
        while node is not None:
            if node in seen_nodes:
                break
            seen_nodes.add(node)
            node = node.next
            
        return node
```

だいぶすっきりした。
Linked List Cycleでもあった、2 pointerを用いた方法も書こうとしたが、追いついたところが the head of a linked list になるわけではなく厳しい。slow_pointerの動いた数を保存しておいて、1つずつ前にずらせばどこかで答えに当たる。2分探索すればそれほどかからないが、正攻法ではないように思える。
考えてもわからなかったため、パス

#### step2

[142. Linked List Cycle II by nanae772 · Pull Request #3 · nanae772/leetcode-arai60 · GitHub](https://github.com/nanae772/leetcode-arai60/pull/3/commits/daebeaf86ef90c97cd2c25eb862f8f64fbc30a55)

- slowとfastが出会った場所を基準とする。
- slow_pointerが辿った道のりのうち、ループに含まれていない長さをx, 含まれている長さをyとすると、
    -  slow_pointerが辿った道のり = x + y
- fast_pointerが辿った道のりのうち、slow_pointerが辿っていない長さをzとおくと
    - fast_pointerが辿った道のり = x + y + z + y
- ここで、fast_pointerが辿った道のり = slow_pointerが辿った道のり* 2であることから、
    - x = z
- よって、headとslowを進めて、邂逅した場所が the head of a linked list.
- 紙に書けばわかったなぁという感想。画面の中で解決しにいったのが良くない

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return None
        slow_pointer = head.next
        fast_pointer = head.next.next
        
        while slow_pointer is not fast_pointer:
            if fast_pointer is None or fast_pointer.next is None:
                return None
            slow_pointer = slow_pointer.next
            fast_pointer = fast_pointer.next.next
        
        while head is not slow_pointer:
            head = head.next
            slow_pointer = slow_pointer.next
        
        return head
```


ほかに見たもの
- [142. Linked List Cycle II by kazizi55 · Pull Request #2 · kazizi55/coding-challenges · GitHub](https://github.com/kazizi55/coding-challenges/pull/2)
- [142. Linked List Cycle II by t-ooka · Pull Request #9 · t-ooka/leetcode · GitHub](https://github.com/t-ooka/leetcode/pull/9/files)

#### step3
set()の方法はLinked List Cycleとほぼ変わらないので、slow and fastで書く
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return None
            
        slow_pointer = head.next
        fast_pointer = head.next.next
        
        while slow_pointer is not fast_pointer:
            if fast_pointer is None or fast_pointer.next is None:
                return None
            slow_pointer = slow_pointer.next
            fast_pointer = fast_pointer.next.next
        
        while head is not slow_pointer:
            head = head.next
            slow_pointer = slow_pointer.next
        
        return head
```
2min
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return None
        
        slow_pointer = head.next
        fast_pointer = head.next.next
        
        while slow_pointer is not fast_pointer:
            if fast_pointer is None or fast_pointer.next is None:
                return None
            slow_pointer = slow_pointer.next
            fast_pointer = fast_pointer.next.next
        
        while head is not slow_pointer:
            head = head.next
            slow_pointer = slow_pointer.next
        
        return head

```
2min

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return None
        
        slow_pointer = head.next
        fast_pointer = head.next.next
        
        while slow_pointer is not fast_pointer:
            if fast_pointer is None or fast_pointer.next is None:
                return None
            slow_pointer = slow_pointer.next
            fast_pointer = fast_pointer.next.next
        
        while head is not slow_pointer:
            head = head.next
            slow_pointer = slow_pointer.next
            
        return head
```
2min
