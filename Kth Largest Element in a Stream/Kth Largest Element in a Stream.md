### Kth Largest Element in a Stream_leetcode

#### step1

 - 雑に書けば毎回sortしてk-1番目を返せばいい。が、無駄がおおい。
- K番目の値を取れるsortedな構造で、１つ数をいれるという操作をO(logN〜N)(NはKth Largestの要素の数)でできるような構造はあるか。
- addがO(N)で良ければsortしたListで保存して、二分探索して場所を決めて、list.insert()すればいい。
- Kが固定されている以上、それより下は保存しなくていい。-> 優先度付きQueueでaddがO(logN)行けそう
    - 構築段階ではsortしてtop Kだけ保存する。
    - 優先度付きキューにtop K個だけ突っ込む
    - 新しいのが入るたびに最小をpopしてK番目に大きい（一番小さい）ものをみてreturn
    - init段階でK個与えられているとは限らないことに注意
- この方針で書く

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        nums = sorted(nums)
        self.nums = nums[max(0,len(nums)-k):]
        self.shortage = k - len(self.nums)
        heapq.heapify(self.nums)


    def add(self, val: int) -> int:
        heappush(self.nums, val)
        if self.shortage > 0:
            self.shortage -= 1
        else:
            heapq.heappop(self.nums)
        return self.nums[0]

```

- 素直な方針でも書いておく

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = nums

    def add(self, val: int) -> int:
        self.nums.append(val)
        self.nums = sorted(self.nums)
        return self.nums[len(self.nums)-self.k]
```

#### step2

https://leetcode.com/problems/kth-largest-element-in-a-stream/solutions/5624035/min-heap-easy-solution-12ms-beats-98
- 似た方針だが、どれくらいkに足りないかをinitとaddで管理しており、こちらのほうが素直だと思う。
- 問題文に書いていないが、k番目は存在することが前提となっているがそれでよいのだろうか。

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = nums
        heapq.heapify(self.nums)
        while len(self.nums) > k:
            heapq.heappop(self.nums)

    def add(self, val: int) -> int:
        heappush(self.nums, val)
        if len(self.nums) > self.k:
            heapq.heappop(self.nums)
        return self.nums[0]
```

https://github.com/skypenguins/coding-practice/pull/23/commits/21a5818eb5b625e0b1f3f3192e8b46730861a1a8#r2348753336
- k, numsを何も考えずにそのままインスタンス変数にしてしまったが、これも命名を考えたほうがいい。
- ここの推奨のように`top_k_scores`がわかりやすい。
- kは意味づけが難しくこのままとする。

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.top_k_scores = nums
        heapq.heapify(self.top_k_scores)
        while len(self.top_k_scores) > k:
            heapq.heappop(self.top_k_scores)

    def add(self, val: int) -> int:
        heappush(self.top_k_scores, val)
        if len(self.top_k_scores) > self.k:
            heapq.heappop(self.top_k_scores)
        return self.top_k_scores[0]
```

https://github.com/5103246/LeetCode_Arai60/blob/703-kth-largest-element-in-a-stream/703-kth-largest-element-in-a-stream/703-kth-largest-element-in-a-stream.md
- initするときに`self.top_k_scores = nums`として全部をインスタンス変数に一度突っ込むよりはstep1のようにスライスして小さくしたほうが k<<len(nums)の場合にいいという話
- mapで実装する方法も記載されていた。同じ要素が繰り返される場合には効率が良さそうだが、K番目のreturn のたびに足し算をする必要があるのが重いように見えた。

#### step3
- 名前をself.nums -> self.top_k_scoresに変更
- self.top_k_scoresの前にスライスで切っておく
を採用してstep3.

```python
class KthLargest:
    
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        nums = sorted(nums)
        self.top_k_scores = nums[max(0, len(nums)-k):]    
        heapq.heapify(self.top_k_scores)

    def add(self, val: int) -> int:
        heapq.heappush(self.top_k_scores, val)
        if len(self.top_k_scores) > self.k:
            heapq.heappop(self.top_k_scores)
        return self.top_k_scores[0]

```
- numsのsortし忘れやadd()でself.top_k_scores.append()してしまい２敗
