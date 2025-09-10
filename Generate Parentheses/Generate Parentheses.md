### Generate Parentheses_leetcode

#### step1

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        # n-1のパターンのどこかに()を足したものがnのパターン
        # 重複が生まれるので、setで管理
        parentheses = [{""}]
        for i in range(n):
            tmp_set = set()
            for pattern in parentheses[i]:
                for index in range(len(pattern)+1):
                    tmp_set.add(pattern[:index]+"()"+pattern[index:])
            parentheses.append(tmp_set)
        return list(parentheses[-1])
```

- 解けはしたが、変数名がひどいのとネストがひどい
- n-1のパターンからnのパターンを生み出す場所を切り出して、再帰でまとめるのが良さそう
- tmp_setはなくしてparentheses[i+1]に直接つっこんで良さそう。

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        parentheses = [set() for i in range(n+1)]
        parentheses[0].add("")
        for i in range(n):
            for pattern in parentheses[i]:
                for index in range(len(pattern)+1):
                    parentheses[i+1].add(pattern[:index]+"()"+pattern[index:])
        return list(parentheses[-1])
```

- 変数iとindexのかぶりが取れない。
- 再帰で書いてみる
```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        def generate_parenthesis_helper(i: int) -> List[str]:
            if i == 0:
                return [""]
            result = set()
            for pattern in generate_parenthesis_helper(i-1):
                for index in range(len(pattern)+1):
                    result.add(pattern[:index]+"()"+pattern[index:])
            return list(result)

        return generate_parenthesis_helper(n)
```

- 結局`for i in range(n)`のiが引数になっただけで変数名の根本は解決していない。思いつかないのでギブアップ
#### step2

[Generate Parentheses by KentaroJay · Pull Request #15 · KentaroJay/Leetcode · GitHub](https://github.com/KentaroJay/Leetcode/pull/15/files)
- dfsで解いている。こちらのほうが素直に見える。使ったleftの数, rightの数, 現在構築済みのstring をstateとして持っておいて深さ探索
- 以下のコードは初めて知ったのでメモ
```python
import code
code.interact(local=locals()) #intractive mode
```
```python
if __name__ == "__main__":
    solution = Solution()
    assert solution.generateParenthesis(1) == ["()"] #このassertの使い方
```

- `yield`は使ったことないかもしれない

[22. Generate Parentheses by potrue · Pull Request #53 · potrue/leetcode · GitHub](https://github.com/potrue/leetcode/pull/53/commits/7f722ae570c53e6c6de9d88e8e3de5ff4bab439c)
- こちらは再帰でDFS

[Create 22. Generate Parentheses.md by tokuhirat · Pull Request #53 · tokuhirat/LeetCode · GitHub](https://github.com/tokuhirat/LeetCode/pull/53/commits/443c5d4f1e3783014d033b2a4cf1188227466066)
- generate_helper() -> Noneで文字列を作りきったらresultに突っ込む形式。慣れている言語ということもあるが理解しやすかった。真似てstep2を書く

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        result = []
        def generate_parentheses_helper(used_open_parentheses: int, used_close_parentheses: int, generated_parentheses: List[str]) -> None:
            if used_open_parentheses == n and used_close_parentheses == n:
                return result.append("".join(generated_parentheses))
            
            if used_open_parentheses > used_close_parentheses:
                generated_parentheses.append(")")
                generate_parentheses_helper(used_open_parentheses, used_close_parentheses + 1, generated_parentheses)
                generated_parentheses.pop()
            
            if used_open_parentheses < n:
                generated_parentheses.append("(")
                generate_parentheses_helper(used_open_parentheses + 1, used_close_parentheses, generated_parentheses)
                generated_parentheses.pop()
                
        generate_parentheses_helper(0, 0, [])
        return result

```

#### step3

再帰で繰り返し書く

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        result = []
        def generate_parenthesis_helper(used_open_parenthesis: int, used_close_parenthesis: int, generated_parenthesis: List[str]) -> None:
            if used_open_parenthesis == used_close_parenthesis == n:
                result.append("".join(generated_parenthesis))
                return
                
            if used_open_parenthesis > used_close_parenthesis:
                generated_parenthesis.append(")")
                generate_parenthesis_helper(used_open_parenthesis, used_close_parenthesis + 1, generated_parenthesis)
                generated_parenthesis.pop()
            
            if used_open_parenthesis < n:
                generated_parenthesis.append("(")
                generate_parenthesis_helper(used_open_parenthesis + 1, used_close_parenthesis, generated_parenthesis)
                generated_parenthesis.pop()
        
        generate_parenthesis_helper(0, 0, [])
        return result

```
