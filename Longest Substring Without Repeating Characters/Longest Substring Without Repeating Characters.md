
```python3
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        # 重複しない文字列の最大の長さを求める
        # 必要な状態→現在見つかっている最大の長さ、文字列のset、今見ている文字列のindex(start, end)
        result = 0
        chars_set = set()
        start_index = 0

        for end_index, end_char in enumerate(s):
            if end_char not in chars_set:
                chars_set.add(end_char)
            else:
                while (s[start_index] != s[end_index]):
                    chars_set.remove(s[start_index])
                    start_index += 1
                start_index += 1
            result = max(result, end_index - start_index + 1)

        return result

```
