### Move Zeroes_leetcode

#### step1
- 0をremoveしてその個数をカウント
- 後でappendする

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        count_zeroes = 0
        while 0 in nums:
            nums.remove(0)
            count_zeroes += 1
            
        for _ in range(count_zeroes):
            nums.append(0)
        return
```
- in-placeはこういうremove and appendもだめで入れ替えのみ許容なのかコピーを作らなければいいのかわからない。
- 0をinfとして扱うようにsort関数を書くのも思いつくところ->他の数字列は保存しないといけないことを忘れていた。
#### step2
- https://leetcode.com/problems/move-zeroes/solutions/6743967/video-two-pointer-solution
- 0とその入れ替え候補のindexを走査してswapを繰り返す方法

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        for i in range(len(nums)):
            if nums[i] == 0:
                for j in range(i,len(nums)):
                    if nums[j] != 0:
                        nums[i], nums[j] = nums[j], nums[i]
                        break

        return
```

- ネストが深いことと二重ループとswapの繰り返しで遅いことが気になる。

https://leetcode.com/problems/move-zeroes/solutions/7200386/one-problem-3-ways-of-easy-solution-in-javascript
- 非zeroをカウントしながらswapで前に詰めてあとから0を詰める方法
    - 言い換えれば0を見つけた時にどことswapすればいいかを保存していく方法。
    - 一つ前と違って２重ループにならないから早くなることが期待できる。
- step1と違い,removeではなくswapで頑張っている

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        non_zero_numbers = 0
        for i in range(len(nums)):
            if nums[i] != 0:
                nums[non_zero_numbers], nums[i] = nums[i], nums[non_zero_numbers]
                non_zero_numbers += 1
        return
```

- 素直に前から詰めていてわかりやすい。
- 計算時間もどこと入れ替えるかをループで検索していない分、一つ前の回答より早い。
- swapにしないで、non_zeroを埋める→zeroを埋めるもやってみる

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        non_zero_numbers = 0
        for num in nums:
            if num != 0:
                nums[non_zero_numbers] = num
                non_zero_numbers += 1
        for i in range(non_zero_numbers, len(nums)):
            nums[i] = 0
        return
        
```

- こちらのほうがわかりやすいと思う。
- 
#### step3
最後の回答を繰り返す

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        non_zero_numbers = 0
        for num in nums:
            if num != 0:
                nums[non_zero_numbers] = num
                non_zero_numbers += 1
        for i in range(non_zero_numbers, len(nums)):
            nums[i] = 0
        return
```
