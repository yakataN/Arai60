
### Word Break_leetcode

https://leetcode.com/problems/word-break/

#### step1

```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        # ex3のようにcatかcatsかどっちをいれればいいのかわからないというようなことが生じる
        # 再帰で書いて実質全探索をするのがよいか
        # state は今見ているところのindexだけでよいか
        # 時間計算量はlen(s) * len(wordDict)?
        def rec(index) -> bool:
            if index == len(s):
                return True
            
            for word in wordDict:
                if s[index:index+len(word)] == word:
                    if rec(index + len(word)):
                        return True
            
            return False

        return rec(0)
```

おそらくあってはいるが、スライスでコピーが作成されるため、大きな例でTLEを起こす。

```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        def rec(index) -> bool:
            if index == len(s):
                return True
            
            for word in wordDict:
                if index + len(word) > len(s):
                    continue
                
                s_slice = ""
                for i in range(len(word)):
                    s_slice += s[index+i]
                if s_slice == word and rec(index + len(word)):
                    return True
            
            return False

        return rec(0)
```

これでもだめなようだ。

前処理としてwordDictがそれぞれどこに当てはまるかを調べておく。長さのみに関心があるので、
index2lengthを作っておく
```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        index_to_length = defaultdict(lambda: [])
        for index in range(len(s)):
            for word in wordDict:
                if s[index:index + len(word)] == word:
                    index_to_length[index].append(len(word))
        # print(index_to_length)

        def rec(index) -> bool:
            if index == len(s):
                return True
            
            for length in index_to_length[index]:
                if rec(index + length):
                    return True
            
            return False

        return rec(0)
```

TLEが抜けない.
indexが踏めるかどうかに関心があり、メモなし再帰だと何度も同じrec(index)を呼び出している事になっているのだと思う。

```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        index_to_length = defaultdict(lambda: [])
        for index in range(len(s)):
            for word in wordDict:
                if s[index:index + len(word)] == word:
                    index_to_length[index].append(len(word))
        # print(index_to_length)

        # memo化 1:到達済み, -1: 未達
        memo = [-1] * len(s)

        def rec(index) -> bool:
            if index == len(s):
                return True
            if memo[index] == 1:
                return False
            memo[index] = 1
            
            for length in index_to_length[index]:
                if rec(index + length):
                    return True
            
            return False

        return rec(0)

```

AC
時間測り忘れたが40分程度

#### step2

##### みたもの

[コーディング練習会典型コメント集(一般社団法人ソフトウェアエンジニアリング協会) - Google ドキュメント](https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.56p9cz7xyy3h)
[139. Word Break by potrue · Pull Request #39 · potrue/leetcode · GitHub](https://github.com/potrue/leetcode/pull/39)
[Solved Arai60/139. Word Break by Satorien · Pull Request #39 · Satorien/LeetCode · GitHub](https://github.com/Satorien/LeetCode/pull/39)
[Word Break by KentaroJay · Pull Request #10 · KentaroJay/Leetcode · GitHub](https://github.com/KentaroJay/Leetcode/pull/10)
- N文字目までの部分文字列は構成できるかでDPするのが正着のよう。自分のようにメモ化再帰でやっている例もあり。
- なんどもスライスを作っていることがよくなさそう
- step1→step2で時間が空いてしまったら、index_to_lengthが何を指すのかわかりにくいと感じた。あるindexから始めた長さNの部分文字列と一致するwordがwordDictにあるかを保存するDict（key: index, value: マッチしたNの配列）の変数名を後からでもわかるようにつけるのは難しい。start_index_to_match_lengths 等になるのか
    - そもそも、valueが配列になっている（Matchしたwordの長さ）のも読みにくさを助長している。
- memo化でtrue, false, unknown分けたくなった結果、memoが読みにくくなっている。配列ではなくDictにすればunknownはkeyに存在しない、true, falseはvalueをtrue, falseにすることで読みやすく実装できると思う。
- 総じて状態の保存に関してはDPのほうが見通しが良い印象。

##### やること
- DPで解く
- メモ化再帰で解き直す

##### DP
```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        # dp[index] = is_reached?(True | False)
        # 0文字マッチを表現する都合、1-index
        dp = [False] * (len(s)+1)
        dp[0] = True

        for i in range(len(s)+1):
            if dp[i] == False:
                continue
            for word in wordDict:
                if s[i:i+len(word)] == word:
                    dp[i+len(word)] = True
        
        if dp[len(s)] == True:
            return True
        return False
```

##### メモ化再帰で解き直す

```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        start_index_to_match_lengths = dict()
        for i in range(len(s)):
            for word in wordDict:
                if s[i:i+len(word)] == word:
                    if i not in start_index_to_match_lengths:
                        start_index_to_match_lengths[i] = set()
                    start_index_to_match_lengths[i].add(len(word))

        memo = dict()

        def rec(index: int) -> bool:
            if index in memo:
                return False
            if index > len(s):
                return False
            if index == len(s):
                return True

            memo[index] = True
            if index not in start_index_to_match_lengths:
                return False
            
            for length in start_index_to_match_lengths[index]:
                if rec(index + length):
                    return True
            return False

        return rec(0)
```


##### やったうえでの感想

- DPのほうが見通しよいし、読みやすい
- $dp[i+len(word)]$でout of rangeになりうることを考えていなかった。範囲外に行く場合は$if s[i:i+len(word)] == word:$で弾かれるようになっているため、問題はなかったが、意識外にあるのは良くなかった。
- スライスは使ってしまっている。Python3における部分文字列の一致はこれでいいのか？

#### step3

```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        dp = [False] * (len(s)+1)
        dp[0] = True

        for i in range(len(s)):
            if dp[i] == False:
                continue
            for word in wordDict:
                if s[i:i+len(word)] == word:
                    dp[i+len(word)] = True

        if dp[len(s)] == True:
            return True
        return False
```

2:19

→コーディング規約では数字を除いて、 “implicit” falseが推奨されていた。(数字ではNoneと0が区別つかないため明示することが推奨される)

[styleguide \| Style guides for Google-originated open-source projects](https://google.github.io/styleguide/pyguide.html#2144-decision)

書き直し

```python3
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        dp = [False] * (len(s)+1)
        dp[0] = True

        for i in range(len(s)):
            if not dp[i]:
                continue
            for word in wordDict:
                if s[i:i+len(word)] == word:
                    dp[i+len(word)] = True

        if dp[len(s)]:
            return True
        return False
```
