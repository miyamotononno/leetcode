## 取り組み方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。まずAIにレビューしてもらう。その後、ほかの人のコードも読む。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。
- step4: いただいたレビューを元に修正する。

## step1
1. 2重ループで解けるものの `Follow-up: Can you come up with an algorithm that is less than O(n2) time complexity?`なのでそれはダメ.(ここまで30s)
2. 次にHashMapでkeyを配列の各値、valueをそのインデックスにすれば値の追加/検索をO(1)、配列を走査するのでtotalでO(n)でできることに気づく。コードを書き始めて、値が重複した場合に上書きされてしまうので、値に対して複数個管理できるデータ構造が必要であることに気づく。(ここまで1分)
3. setかlistのどちらで管理するかで2,3分悩み、結局listを選択する。理由は、`You may assume that each input would have exactly one solution`により値が３個以上になることはあり得ないこと、高々2個の要素でその二つを簡単に取得できるのはlistだと思ったから。重複する値が非常に多いケースでも、listの場合、末尾に要素を追加する操作の償却時間計算量はO(1)でありindexによる値の取得はO(1)である一方、setの場合は、値の追加操作にO(log n)かかり、最初の二つの要素の取得がone-lineで書けなさそうだと考えた。
4. 実装終わり(以下のコード)。合計7分半。Easy問題なのでもっと簡潔に書くやり方がありそうだが思いつかなかった。後、いい変数名が思いつかなかった。

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        value_to_idx = {}
        for idx, n in enumerate(nums):
            if n in value_to_idx:
                value_to_idx[n].append(idx)
            else:
                value_to_idx[n] = [idx]
        for idx, n in enumerate(nums):
            rest = target - n
            if rest != n and rest in value_to_idx:
                return (idx, value_to_idx[rest][0])
            if rest in value_to_idx:
                if len(value_to_idx[n]) > 1:
                    return (value_to_idx[n][0], value_to_idx[n][1])
```

## step2
1. ChatGPTに聞く。それまでに走査した値を保持し、補数がdictに存在するかをその場で確認すれば一回のfor文でかけることに気づく。加えて、`You may assume that each input would have exactly one solution`という仮定により、複数個のindexを持つ必要がない。ChatGPTにいい変数名がないか聞いたら、value_to_idxはseenやlookup, restはcomplementでも代用できるのはとのこと。それを踏まえて書いたコードは以下の通り。1分弱で書いた。

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen = {}
        for i, n in enumerate(nums):
            diff = target - n
            if diff in seen:
                return [seen[diff], i]
            seen[n] = i
```
2. 他の人のコードを見る。
- https://github.com/garunitule/coding_practice/pull/11/files
for文から抜けても、解が見つからない場合にExceptionを吐き出している。`You may assume that each input would have exactly one solution`という前提があるのでそんなことはないと思って書いてなかったが、前提を知らないレビュアーが見たとき混乱するだろう。
https://docs.python.org/ja/3/library/exceptions.html#ValueError をチェックし、`演算子や関数が、正しい型だが適切でない値を持つ引数を受け取ったときや、 IndexError のようなより詳細な例外では記述できない状況で送出されます。`という記述からValueErrorがExceptionの中で適切であることを確認する。
別の方で空配列や(-1, -1)と返す実装もあったが、明らかに異常系であることからExceptionをthrowすることを選択する。
- https://github.com/ryoooooory/LeetCode/pull/18/files#r1729076421
`キーと値がどのような値か連想できるような変数にすることをお勧めいたします。`とあるのでseenやlookupの代わりに、num_to_idxの変数名に変更しようと考えた。さらに`rest`という変数名もアバウトすぎる(rest: あるものの集合の中からいくつかをとった残りというイメージ)。`complement`は補数なのでこの変数を表現するのにぴったりである。この方は`pairNum`を用いており、それもわかりやすいなと思った。

## step3
最終的な回答は以下の通り。

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_idx = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_idx:
                return (i, num_to_idx[complement])
            num_to_idx[num] = i
        
        ValueError("Never reached here due to the presumption that each input would have exactly one solution.")

```

## step4
```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {} # Avoid abbreviation
        for index, num in enumerate(nums): # use index instead of i
            complement = target - num
            if complement in num_to_index:
                return (index, num_to_index[complement])
            num_to_index[num] = index
        
        raise ValueError("Never reached here due to the presumption that each input would have exactly one solution.") # need to raise!
```