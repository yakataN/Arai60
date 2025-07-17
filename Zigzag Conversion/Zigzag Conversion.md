
### Zigzag Conversion_leetcode

#### step1
文字列とNが渡されて順番を規則的に動かすもの
方針１：個数NのVector（作成時点では配列長不明な配列）を作り、それぞれにPushしていく方式。Python3のstrの配列はこの条件を満たす。Stringのほうが簡便か。
方針２：
それぞれの個数を計測して固定した配列を作り2n-2を周期として固定長のchar配列を作る。ヒープに頼らないというメリットがあるが、配列長を並べるまでが複雑
方式１で作る

変数
この全体のcharらの状態を表す変数名が難しい。stringを並び替えた中間状態。それぞれの段のcharをstrで表したものと言うことを表現したい。
並び替え方は失うがreordered_chars_list でよいか。あるいはzigzaged_chars_list
numRowsが１である場合があることに注意

```python3
class Solution:
    def zigzag_solution(self, s: str, numRows: int) -> str:
        reordered_chars_list = [""] * numRows
        height = 0
        isForward = true
        for char in s:
            reordered_chars_list[height] += char
            if numRows == 1:
                continue
            else if height == numRows - 1:
                height -= 1
                isForward = false
            else if height == 0:
                height = 1
                isForward = true
            else if isForward:
                height += 1
            else:
                height -= 1
        return "".join(reordered_chars_list)
```

true, falseを小文字で書いたのとelse if→elifのミスで２回エラー
```python3
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        reordered_chars_list = [""] * numRows
        height = 0
        isForward = True
        for char in s:
            reordered_chars_list[height] += char
            if numRows == 1:
                continue
            elif height == numRows - 1:
                height -= 1
                isForward = False
            elif height == 0:
                height = 1
                isForward = True
            elif isForward:
                height += 1
            else:
                height -= 1
        return "".join(reordered_chars_list)
```

AC

#### step2

- ifが多くみにくい
- numRows=1は処理が特別のため、もっと早く払い出していいのではないか（変数宣言の前でいい）
- 端っこにあたったらisForwardを反転させる処理とheightを進める処理は分けられる。
- 前に行くかどうかの処理は -1+2\*isForwardとかすればまとめられるがこっちのほうがみにくい

```python3
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        reordered_chars_list = [""] * numRows
        height = 0
        isForward = True
        for char in s:
            reordered_chars_list[height] += char
            if height == 0 or height == numRows - 1:
                isForward = !isForward
                
            if isForward:
                height += 1
            else:
                height -= 1
        return "".join(reordered_chars_list)
```

初手が0のためheightが−１になってしまった。修正

```python3
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        reordered_chars_list = [""] * numRows
        height = 0
        isForward = True
        for char in s:
            reordered_chars_list[height] += char
            if (height == 0 and not isForward) or (height == numRows - 1 and isForward):
                isForward = not isForward
                
            if isForward:
                height += 1
            else:
                height -= 1
        return "".join(reordered_chars_list)
```

step2他の人を見る
[Create 6. Zigzag Conversion.md by fuga-98 · Pull Request #57 · fuga-98/arai60 · GitHub](https://github.com/fuga-98/arai60/pull/57)
- 問題通りに２次元に落とし込んでいる。実業務では仕様変更がありうることを考えると、実直な対応も一度は考えたほうがいい。

```python3
# 実直な実装
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        zigzag_map = [[""] * len(s) for _ in range(numRows)]
        
        # stateとしてindex, 現在地(row, column)を管理
        index = 0
        row = 0
        column = 0
        while index < len(s):
            # 下に行く
            while row < numRows and index < len(s):
                zigzag_map[row][column] = s[index]
                index += 1
                row += 1
            row -= 2
            column += 1
            # 上に行く
            while row > 0 and index < len(s):
                zigzag_map[row][column] = s[index]
                index += 1
                row -= 1
                column += 1
        
        # 出力
        ret = ""
        for rows in zigzag_map:
            for char in rows:
                if char != "":
                    ret += char
        return ret
```

- ながくなるので、上下は関数切り出してもいい

```python3
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        
        if numRows == 1:
            return s
        zigzag_map = [[""] * len(s) for _ in range(numRows)]
        
        def move_down(index, column) -> int:
            row = 0
            while row < numRows and index < len(s):
                zigzag_map[row][column] = s[index]
                index += 1
                row += 1
            return index
        
        def move_up_diagonal(index, column) -> int:
            row = numRows - 2
            column += 1
            while row > 0 and index < len(s):
                zigzag_map[row][column] = s[index]
                index += 1
                row -= 1
                column += 1
            return index, column

        # stateとしてindex, 現在地(row, column)を管理
        index = 0
        row = 0
        column = 0
        while index < len(s):
            index = move_down(index, column)
            index, column = move_up_diagonal(index, column)
        
        # 出力
        ret = ""
        for rows in zigzag_map:
            for char in rows:
                if char != "":
                    ret += char
        return ret
```

- 関数の副作用でzigzag_mapを更新しているのが気になる。
[6. Zigzag Conversion by skypenguins · Pull Request #5 · skypenguins/coding-practice · GitHub](https://github.com/skypenguins/coding-practice/pull/5)
- direction = 1 or -1としてそのまま足しているのがわかりやすい
```python3
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        reordered_chars_list = [""] * numRows
        height = 0
        direction = 1
        for char in s:
            reordered_chars_list[height] += char
            if (height == 0 and direction == -1) or (height == numRows - 1 and direction == 1):
                direction *= -1
            height += direction
        return "".join(reordered_chars_list)
```

こうなるなら足してしまったら範囲を外れるのほうが直感的
```python3
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        reordered_chars_list = [""] * numRows
        height = 0
        direction = 1
        for char in s:
            reordered_chars_list[height] += char
            if height + direction < 0 or height + direction >= numRows:
                direction *= -1
            height += direction
        return "".join(reordered_chars_list)
```


#### step3

```python3
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 0:
            return s

        reordered_chars_list = [""] * numRows
        height = 0
        direction = 1
        for char in s:
            reordered_chars_list[height] += char
            if height + direction < 0 or height + direction >= numRows:
                direction *= -1
            height += direction
        return "".join(reordered_chars_list)
```

計測忘れ, 3:12, 1:52
