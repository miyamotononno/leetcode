- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。まずAIにレビューしてもらう。その後、ほかの人のコードも読む。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。
- step4: いただいたレビューを元に修正する。

## step1
1. 以前一回解いたことがあり、stackを使えば解けることは知っていた。6分で書き、1分テストケースが通るかを確かめるのに時間を使った。

```py
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:
        closed_to_open = {
            '}': '{',
            ']': '[',
            ')': '('
        }
        stack = deque()
        for letter in s:
            if letter in closed_to_open:
                if len(stack) == 0:
                    return False
                top = stack.pop()
                if top != closed_to_open[letter]:
                    return False
            else:
                stack.append(letter)

        return len(stack) == 0
```

## step2
### AIによるレビュー
- AIにレビューをお願いしたところ、`len(stack) == 0`より`not stack`の方が可読性・Pythonスタイルの両面からいいらしい。Python では list, deque, dict などの空のコンテナは False として評価されるため、わざわざ`len(...) == 0`と書かずに、Boolean context で直接扱うのが自然で読みやすいとのこと。
```
これは可読性・Pythonスタイルの両面からよく使われます。
Style Guide より補:
   Booleans should be used directly. Use if not x instead of if len(x) == 0.
```
- 要素がある場合にtrueを返してほしい場合、`return stack`でも正しいが呼び出し元が期待しているTrue/Falseを確実に返すために`return bool(stack)`で明示的に型を保証する(Pythonの型ヒントは静的解析ツール向けであるため実行時にはなんの制約もない)。

### 他の型のコードを読む
- https://github.com/skypenguins/coding-practice/pull/11
- https://github.com/ryosuketc/leetcode_arai60/pull/6

これらのレビューを見て自分のコードの変数名が読みにくいことに気付く。
- `stack` -> `unmatched_open_brackets` (意味がわからない上に、Style Guideによれば`Avoid names that needlessly include the type of the variable.`として型を変数名に含めるのは原則避ける)
- `closed_to_open` -> `opening_to_closing_brackets`（開きかっこ->閉じかっこの方がわかりやすい）
- `letter` -> `bracket` (`letter`はやや抽象的。)

## Step3
最終的には３分ぐらいで書けるようになった
```py
from collections import deque

class Solution:
    def isValid(self, s: str) -> bool:
        opening_to_closing_brackets = {
            '(': ')',
            '{': '}',
            '[': ']'
        }
        unmatched_opening_brackets = deque()
        for bracket in s:
            if bracket in opening_to_closing_brackets:
                unmatched_opening_brackets.append(bracket)
            else:
                if not unmatched_opening_brackets:
                    return False
                latest_opening_bracket = unmatched_opening_brackets.pop()
                if opening_to_closing_brackets[latest_opening_bracket] != bracket:
                    return False
        
        return not unmatched_opening_brackets
```
