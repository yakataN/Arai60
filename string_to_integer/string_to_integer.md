## step1

```python3
import re

class Solution:
    def myAtoi(self, s: str) -> int:
        # start  15:02
        # アルゴリズムをそのまま実装できるかのテストとしてみて良さそう 
        # 与えられているテストけーすでは足りないから" 42"を追加

        # 1
        while (len(s)>0 and s[0]==' '):
            s = s[1:]

        # 2 Signedness
        is_positive = True
        if len(s) > 0 and s[0] == '-':
            is_positive = False
            s = s[1:]
        elif len(s) > 0 and s[0] == "+":
            s = s[1:]
        
        # 3
        tmp_s = ""
        pattern = re.compile(r"^\d")

        while (pattern.match(s)):
            tmp_s += s[0]
            s = s[1:]

        if tmp_s == "":
            result = 0
        else:
            result = int(tmp_s)
        
        if not is_positive:
            result *= -1
        # 4
        if result < -1 * 2 ** 31:
            result = -1 * 2 ** 31
        if result > 2 ** 31 - 1:
            result = 2 ** 31 - 1
        
        return result

        # 15:26おわり
        # re を忘れていたのと、空文字の処理に困った。
        # 空文字のときの処理を毎回入れている今回の処理でいいのか
        # とりあえず解いただけであり、whitespaceがグチャくちゃ
        # and と&&の優先度が怪しい
```

24min

