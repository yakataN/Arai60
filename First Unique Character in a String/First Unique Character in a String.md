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
