### Is Subsequence

https://leetcode.com/problems/is-subsequence

#### step1
```python3
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        # 先頭からみて同じものがあれば消費する。最後までindexを回せたら終わり
        # 終了判定を挟む必要があり、そこをわかりやすくかけるかどうかでindexの扱いを変えたい
        if len(s) == 0:
            return True
        seen = 0
        for c in t:
            if s[seen] == c:
                seen += 1
            if seen == len(s):
                return True
        return False
```

#### step2
[Arai60/392. Is Subsequence.md at 0aac26e68fa6b8f516ff494da34578b8cc1cefc2 · olsen-blue/Arai60 · GitHub](https://github.com/olsen-blue/Arai60/blob/0aac26e68fa6b8f516ff494da34578b8cc1cefc2/392.%20Is%20Subsequence.md#%E8%A7%A3%E6%B3%952%E4%BB%95%E4%BA%8B%E3%81%AE%E5%BC%95%E3%81%8D%E7%B6%99%E3%81%8E%E3%83%88%E3%83%83%E3%83%97%E3%83%80%E3%82%A6%E3%83%B3%E5%86%8D%E5%B8%B0dpac)
再帰で書くループ
やっていることはstep1と同じだが、再帰苦手なので、こっちでも実装する

```python3
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        def traverse(s_index, t_index) -> bool:
            if s_index == len(s):
                return True
            if t_index == len(t):
                return False

            if s[s_index] == t[t_index]:
                return traverse(s_index + 1, t_index + 1)
            else:
                return traverse(s_index, t_index + 1)
        return traverse(0, 0)
```
return traverse(s_index + 1, t_index + 1)のreturnをつけ忘れて1敗

再帰をwhile True:で書き直す方法も真似る
```python3
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        s_index = 0
        t_index = 0

        while True:
            if s_index == len(s):
                return True
            if t_index == len(t):
                return False
            
            if s[s_index] == t[t_index]:
                s_index += 1
                t_index += 1
            else:
                t_index += 1
```


[392. Is Subsequence by fhiyo · Pull Request #55 · fhiyo/leetcode · GitHub](https://github.com/fhiyo/leetcode/pull/55/files#diff-a6c7d5ff748fd033529b0b0a550ed2aa570e18edc3e2c61da5094aec0e23a91eR45-R46)
- 二分探索法
- s<<tでかつ、sが違う処理が入るときにtを前処理して置きたいという気持ちから
- 計算量はO(len(s)loglen(t))でO(len(t))よりは短くなる状況はありそう。

#### step3
```python3
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        if s == "":
            return True
        
        seen = 0
        for char_t in t:
            if s[seen] == char_t:
                seen += 1
            if seen == len(s):
                return True
        return False
```

1:14, 57, 56sec
