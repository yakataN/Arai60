### Linked List Cycle_leetcode

#### step1

測っていないが、step1で２０分程度かかったと思われる。

##### 考え
- OptionalにO(1)とあるが、思いつかなかったので、素直にnextをたどり続ける方針とした。
- Optional[T]型の処理を忘れていて手間取った（Optional[T] はT | None のalias)
- head.nextの型がListNodeでいいのか、また、それをどう確かめればいいのかわからず手間取った
```python
print(head.__class__)
print(head.next.__class__)
```
で良かったようだ。実際はc++のclassコメント(下記)を見てhead.nextの型がListNode(c++ではそのポインタ)であること把握した。

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```

##### ACコード
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        flag = False
        pos_set = set()
        while True:
            if head is None:
                break
            if head in pos_set:
                flag = True
                break
            pos_set.add(head)
            head = head.next
        
        if flag is True:
            return True
        return False

```

##### 反省
- flag処理
- 問題文のposに引っ張られてpos_setという変数名にしたが、node_setのほうが実態である。(過去に内部構造ではなく、役割を変数名にしたほうがいいと指摘があったが、node_setではそれが出来ていない。)(step2で直す）

#### step2

nodeの数をNとおく

- https://leetcode.com/problems/linked-list-cycle/solutions/5625166/3-easy-and-simple-c-approaches-two-pointer-hash-map-beginner-friendly
    - Approach1, 2
        - slow and fast pointer approach.
        - １つずつ進むポインターと２つ進むポインターを用意して邂逅したらcycleであると判定
        - 最大でもslowが2N回進むまでに邂逅するからO(N)
        - step1と違い空間計算量がO(1)で済むため、nodeの数がscaleした場合に優しい。
        - 時間計算量の差もstep1と比較して定数倍レベル（step1はcycleに入った瞬間に気づける）であり、データ構造を全体集合に触れられない連結リストで出題されている以上、こっちのほうが正着に見える。
        - 下記実装では変数名は参照元に準じてslow, fastにしてしまったが、これでいいのか。一応変数名は外から見た振る舞いにはなっていると思う。
    - Approach 3
        - node_set（変数名）をvisitedにしている。こちらのほうがnode_setに比べて何を表しているかがわかってよい。取り入れる。

##### setを使った書き方(step1書き直し)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        # ここのif文はなくていいことにstep3で気づく
        if head is None:
            return False

        visited = set()
        while True:
            if head is None:
                return False
            if head in visited:
                return True
            visited.add(head)
            head = head.next
            
```

##### 2pointer Approach

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if head is None or head.next is None:
            return False
        slow = head
        fast = head.next

        while slow is not fast:
            if fast.next is None or fast.next.next is None:
                return False
            slow = slow.next
            fast = fast.next.next
        return True

```

[141. Linked List Cycle by yosukekato165 · Pull Request #2 · yosukekato165/leetcode · GitHub](https://github.com/yosukekato165/leetcode/pull/2/commits/8711e532f25536fe8bc3fc280f841ab988ebd100)
- 孫引きだが、2pointer アプローチは常識ではないらしいので、setを使った書き方でstep3を書く

#### step3

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        while True:
            if head is None:
                return False
            if head in visited:
                return True
            visited.add(head)
            head = head.next

```
#1:22 #0:55 #0:40 

#### コメント

- step2→step3でBlank Lineを使うかどうかがブレた
- https://google.github.io/styleguide/pyguide.html#35-blank-lines
  > Use single blank lines as you judge appropriate within functions or methods.
- 常識はどこに空行をいれるのだろうか
