## 取り組み方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。まずAIにレビューしてもらう。その後、ほかの人のコードも読む。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。
- step4: いただいたレビューを元に修正する。

## step1
以前一回解いたことがあったため、Two Pointer algorithmが解法なことは知っていた。4分半で解いた。

```py
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if head is None:
            return False
        
        fast_node = head.next
        slow_node = head
        while fast_node and slow_node:
            if fast_node is slow_node:
                return True
            if fast_node.next is None:
                return False

            fast_node = fast_node.next.next
            slow_node = slow_node.next

        return False
```

## step2
### AIによるレビュー
前回のコメントを元に、プロンプトでGoogle Python Style Guideに従うように指示してレビューするようにお願いする。
まず、`fast, slowは意味の明確な短縮形として業界標準のネーミングです。冗長な *_node を避けた方が読みやすくなります。`と指摘されたので、それに従う。改善済みのコードとして以下のコードを提示されたが、

```PY
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not head or not head.next:
            return False

        slow = head
        fast = head.next
        while fast and fast.next:
            if slow is fast:
                return True
            slow = slow.next
            fast = fast.next.next

        return False
```
`if not head`か `if head is None`のどちらのほうがいいか気になったので聞いてみる。するど、

| 書き方                                     | 意味                      | Pythonicか  | GSG準拠か | コメント         |
| --------------------------------------- | ----------------------- | ---------- | ------ | ------------ |
| `if not head or not head.next:`         | None や False-like をチェック | ✅ Yes      | ✅ Yes  | 簡潔で読みやすい     |
| `if head is None or head.next is None:` | 明示的に None を比較           | ✅ 条件付きでYes | ✅ Yes  | 明示的だが冗長になりがち |

🔍 GSGの観点（Google Python Style Guide）
GSGでは次のように述べられています：
> Prefer if not x to if x is None unless x could be another false-y value that needs to be treated differently.

つまり：
- head や head.next が None のときに 単純な真偽値チェック（not）で足りるなら、それを使え
- None か 0 か "" かを区別したいときだけ is None を使う

結論、今回はheadが`Optional[ListNode]`という型なので単純な真偽値チェックで問題ないだろう。

### 他の方のコードを読む
- https://github.com/ryosuketc/leetcode_arai60/pull/1/files
`slow`や`fast`といった変数名はそのままで良さそう。
コメントで`!=` と `is not`の違いについて言及されていた。
一般的に,**等価性(equality)**、つまり値として等しいかを比較する。一方、`is not`は**同一性(identity)**、メモリ上で同じオブジェクトがどうかを判断する(id()の比較)。等価性は__eq__()の定義、つまり開発者が`==`をどう定義したかで変わるものの、同一性は常にisによるメモリアドレスによる比較である。ちなみに`!=`はまず`__ne__()`を探し、なければ`__eq__()`の結果を反転して使う。

等価性と同一性がわかる具体例を挙げると、
```py
a = [1, 2, 3]
b = [1, 2, 3]
c = a

a == b      # ✅ 等価性：True（内容が同じ）
a is b      # ❌ 同一性：False（別のリスト）

a is c      # ✅ 同一性：True（同じオブジェクト）
```

今回の場合は、当然fastかslowが同一であるかを調べているので、`is`や`is not`を使うのが正しい。


### step3
一分ぐらいで書きあげられるようになった。
```PY
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not head or not head.next:
            return False

        slow = head
        fast = head.next
        while fast and fast.next:
            if slow is fast:
                return True
            slow = slow.next
            fast = fast.next.next

        return False
```