### Valid Parentheses_leetcode

#### step1

```python
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:
        # カッコがうまく閉じているかを確認
        # stackをうまく扱えるかどうかのテストに見える
        # pythonではlistでも実装できるが、趣旨に沿ってdeque(純粋なstackはなかったはず)で実装する

        left_side_of_brackets = ('(', '{', '[')
        taple_of_brackets = (('(', ')'), ('{', '}'), ('[', ']'))

        visited = deque([])
        flag = True
        for c in s:
            if c in left_side_of_brackets:
                visited.append(c)
            else:
                if len(visited) == 0:
                    flag = False
                    break
                if (visited.pop(), c) not in taple_of_brackets:
                    flag = False
                    break
        if flag and len(visited) == 0:
            return True
        return False

```

- tapleの扱いが慣れていなくて時間かかった
- ifのネストが深いからなんとかしたい。
    - elifにすれば一段は下がる
    - flag=Falseの代わりにreturn Falseでいい
    - そうするとelse:以下は早期にreturnできるから上にもっていける。
    - visitedが空の場合のIndex-errorをうまく処理できればここをうまくかけそう。プログラミング経験がAtcoderに偏っているため、エラーハンドリングは苦手分野
- cはcharacterのほうがわかりやすい印象

```python
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:

        left_side_of_brackets = ('(', '{', '[')
        taple_of_brackets = (('(', ')'), ('{', '}'), ('[', ']'))
        visited = deque([])

        for character in s:
            if character not in left_side_of_brackets and (len(visited) == 0 or (visited.pop(), character) not in taple_of_brackets):
                return False

            if character in left_side_of_brackets:
                visited.append(character)
        print(visited)
        if len(visited) == 0:
            return True
        return False

```

- if文の条件が長くなってしまったから外に出したい。
    - cが入っても成り立つかが振る舞い
    - is_valid(c)とする。あんまり良い命名とは思えないが思いつかない。
    
```python
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:

        left_side_of_brackets = ('(', '{', '[')
        taple_of_brackets = (('(', ')'), ('{', '}'), ('[', ']'))
        visited = deque([])

        def is_valid(character):
            return character not in left_side_of_brackets and (len(visited) == 0 or (visited.pop(), character) not in taple_of_brackets)

        for character in s:
            if is_valid(character):
                return False
            if character in left_side_of_brackets:
                visited.append(character)
        print(visited)
        if len(visited) == 0:
            return True
        return False
```

- is_valid()内の可読性が終わっている。もとのようにif文でかいたほうが良さそう
- is_valid()と言いながらTrueならばreturn Falseなのがevil.直す

```python
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:

        left_side_of_brackets = ('(', '{', '[')
        taple_of_brackets = (('(', ')'), ('{', '}'), ('[', ']'))
        visited = deque([])

        def is_valid(character):
            if character not in left_side_of_brackets:
                if len(visited) == 0:
                    return False
                if (visited.pop(), character) not in taple_of_brackets:
                    return False
            return True

        for character in s:
            if not is_valid(character):
                return False
            if character in left_side_of_brackets:
                visited.append(character)
        if len(visited) == 0:
            return True
        return False
```

- is_valid()がvisitedを破壊する副作用を持っているのが気になるが、直せないのでここまでとする。
#### step2

[20. Valid Parentheses.md by konnysh · Pull Request #6 · konnysh/arai60 · GitHub](https://github.com/konnysh/arai60/pull/6/files)

- open_brackets, close_bracketsというらしい。
- bracketsの組を自分はtapleで表現したが、mapの実装が多い。今は３つだからおそらくそれほど差がないが、O(n)とO(logn)で読みやすさにほとんど差がないので、mapが良さそう
- c++でいう top()(stackのlast inしたものを見るだけの機能)が関数一覧になくて上記のような実装となったが、
> `d[0]` のような添字による参照をサポートしています。添字によるアクセスは、両端の要素では _O_(1) ですが、中央部分の要素では _O_(_n_) と遅くなります。
- とのことで、添字で良かったらしい。(引用元: [collections --- コンテナデータ型 — Python 3.13.7 ドキュメント](https://docs.python.org/ja/3/library/collections.html#deque-objects))
- left_side_of_brackets, taple_of_bracketsは定数として扱うためにSCREAMING_SNAKE_CASEにしても良かった。
- 構文解析で解いている人もいた。確かにプログラミング言語の構文解析の簡易版っぽい。`S → "("S")"S | "{"S"}"S | "["S"]"S | ε`でよいか
- 将来の課題: 再帰下降構文解析で解く

```python
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:

        OPEN_BRACKETS = ('(', '{', '[')
        CLOSE_TO_OPEN_BRACKETS = {")": "(", "}": "{", "]": "["}
        visited = deque([])
        
        for character in s:
            if character in OPEN_BRACKETS:
                visited.append(character)
            elif len(visited) != 0 and CLOSE_TO_OPEN_BRACKETS[character] == visited[-1]:
                    visited.pop()
            else:
                return False

        if len(visited) == 0:
            return True
        return False
```

- 変数名characterが型(のようなもの)しか表していないため、いい命名とは言えないが、思いつかなかった。
- CLOSE_TO_OPEN_BRACKETSは改行したほうが読みやすそう

#### step3

```python
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:
        
        OPEN_BRACKETS = ["(", "{", "["]
        CLOSE_TO_OPEN_BRACKETS = {
            ")": "(",
            "}": "{",
            "]": "["
        }
        visited = deque([])
        
        for character in s:
            if character in OPEN_BRACKETS:
                visited.append(character)
            elif len(visited) != 0 and visited[-1] == CLOSE_TO_OPEN_BRACKETS[character]:
                visited.pop()
            else:
                return False
                
        if len(visited) == 0:
            return True
        return False

```

今更だが、`if len(visited)==0`より`if not visited`のほうが良さそう

```python
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:
        
        OPEN_BRACKETS = ["(", "{", "["]
        CLOSE_TO_OPEN_BRACKETS = {
            ")": "(",
            "}": "{",
            "]": "["
        }
        visited = deque([])
        
        for character in s:
            if character in OPEN_BRACKETS:
                visited.append(character)
            elif visited and visited[-1] == CLOSE_TO_OPEN_BRACKETS[character]:
                visited.pop()
            else:
                return False
        
        if visited:
            return False
        return True
```
