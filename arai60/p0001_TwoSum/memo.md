## 取り組み方
- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず5分立ったら答えを見る。答えを送信して正解するまで。
- step2: コードを読みやすく整える。動くコードになったら終了。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。

## step1


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