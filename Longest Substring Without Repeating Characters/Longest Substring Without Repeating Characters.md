
```python3
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        #尺取法で前から見ていって、被ったら前を一つずつ消していき、被った文字がでるところまで消していく
        result = 0
        start_index = 0
        end_index = 0
        set1 = set()
        for end_index, end_char in enumerate(s):
            if end_char not in set1:
                set1.add(end_char)
            else:
                # start_indexからpopして被っているか確認
                while(s[start_index]!=s[end_index]):
                    set1.remove(s[start_index])
                    start_index += 1
                #input[start_index]==input[end_index]のところで止まっているため、start_indexを進める。
                start_index += 1
            result = max(result, end_index - start_index + 1)
        return result
```
