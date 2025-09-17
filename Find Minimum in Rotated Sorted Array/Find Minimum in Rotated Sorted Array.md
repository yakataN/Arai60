### Find Minimum in Rotated Sorted Array

#### step1
- 文頭と文末を見て, 0,1,2,...,Nとなっていれば、すなわちnums[0]<nums[n-1]となっていればrotateせずに順番に並んでいるので、nums[0]を返せばいい
- そうでないときはnums[i] > nums[i+1]となるスポットがあるはずで、このときのnums[i+1]が答え。nums[i+1]は任意のi+1番目を除くnumsの要素より小さいため、適当なnumsの要素(ここではnums[0])を基準として、それより小さいかどうかで二分探索できる。

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        if nums[0] < nums[-1]:
            return nums[0]
        
        target = nums[0]
        left_index = 0
        right_index = len(nums) - 1
        while right_index - left_index > 1:
            mid_index = (left_index + right_index) // 2
            if nums[mid_index] > target:
                left_index = mid_index
            else:
                right_index = mid_index
        return nums[right_index]
```
- targetの変数名がもう少し工夫できそう。

#### step2
https://github.com/KentaroJay/Leetcode/pull/18/files
https://github.com/Satorien/LeetCode/pull/42/files
- 半開区間じゃないコードを読み慣れていない。特に停止するかどうかが読み切れない。
- midはmiddleの略語のため、middleと書いたほうがいい（変数の話）
- 求めるところは配列の最大値->最小値となっているところだから、配列の右端よりも見ている場所（mid)が低ければその範囲には求めるところがないことがわかる。これは最初から単調増加になっている場合でも成り立つ。（場合分けが必要なくなる）
    - また、変数targetが必要なくなる
    - この場合左が開いている半開区間で考えているから、最初から単調増加の場合も考えるならばleft = -1にする必要がある。
- left_index -> left, right_index -> rightにしてもいいかもしれない。上から下に読むことを考えると、この変数はindexとしての振る舞いをしますというのが上の段階でわかるのは嬉しいかもしれないが、２行後にはその使い方をされているので。
    - indexといいつつ、left_index = -1とかくと違和感があるし、python の場合は最終項とも読めるので、ないほうがいいか。
- `x, y = 0, 1`の１行で複数の変数代入するほうが簡便（で関連性もわかりやすい）

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left_index = -1
        right_index = len(nums) - 1
        while right_index - left_index > 1:
            mid_index = (left_index + right_index) // 2
            if nums[mid_index] > nums[right_index]:
                left_index = mid_index
            else:
                right_index = mid_index
        return nums[right_index]
```

#### step3
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left, right = -1, len(nums) - 1
        while right - left > 1:
            middle = (left + right) // 2
            if nums[middle] > nums[right]:
                left = middle
            else:
                right = middle
        return nums[right]
```
- `while left - right > 1`と書いて1敗

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left, right = -1, len(nums) - 1
        while  right - left > 1:
            middle = (left + right) // 2
            if nums[middle] > nums[right]:
                left = middle
            else:
                right = middle
        return nums[right]
```

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left, right = -1, len(nums) - 1
        while right - left > 1:
            middle = (left + right) // 2
            if nums[middle] > nums[right]:
                left = middle
            else:
                right = middle
        return nums[right]
```

2min
