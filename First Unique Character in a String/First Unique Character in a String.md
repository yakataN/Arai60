## step1

```python3
class Solution:
    def firstUniqChar(self, s: str) -> int:
        # uniqueに使われている最初の文字を提示する。
        # 一度dict<char, num(回数)>で全体を走査して、２度目に見たときに回数が１のものが見つかったらreturnする
        char_count_dict = {}
        for char in s:
            if char in char_count_dict:
                char_count_dict[char] += 1
            else:
                char_count_dict[char] = 1
        for index, char in enumerate(s):
            if char_count_dict[char] == 1:
                return index
        
        return -1

```

## step2

２回目以上の情報はいらないから、charをsetでもつものと、deplicate_setで持つ実装を見つけた
出現回数を調べるCounter組み込み関数がある
step1, 2で時間測り忘れている

### 出現回数を調べる実装

```python3
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_set = set()
        deplicate_char_set = set()

        for char in s:
            if char in char_set:
                deplicate_char_set.add(char)
            else:
                char_set.add(char)
        for index, char in enumerate(s):
            if char not in deplicate_char_set:
                return index
        return -1
```

### Counterを使った実装

大文字小文字の区別あり(x collections.countersでは動かない）

```python3
class Solution:
    def firstUniqChar(self, s: str) -> int:
        import collections

        char_to_count_dict = collections.Counter(s)

        for index, char in enumerate(s):
            if char_to_count_dict[char] == 1:
                return index

        return -1
```

## step3

```python3
class Solution:
    def firstUniqChar(self, s: str) -> int:
        import collections

        char_to_count_dict = collections.Counter(s)
        for index, char in enumerate(s):
            if char_to_count_dict[char] == 1:
                return index
        return -1
```
