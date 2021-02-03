---
title: python kth largest
tags: [python, kth_largest]
key: PythonKLargest
---

## Top k Frequent Elements

[leetcode](https://leetcode.com/problems/top-k-frequent-elements/description/) or [力扣中国](https://leetcode-cn.com/problems/top-k-frequent-elements/description/)

### Solution (python3):

```python
from collections import Counter
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        return [i[0] for i in Counter(nums).most_common(k)]
```

Using ```most_common()``` api provided can solve this problem easily.

The source code of ```most_common()```function.

```python
def most_common(self, n=None):
	'''List the n most common elements and their counts from the most
    common to the least.  If n is None, then list all element counts.
    >>> Counter('abcdeabcdabcaba').most_common(3)
    [('a', 5), ('b', 4), ('c', 3)]
    '''
    # Emulate Bag.sortedByCount from Smalltalk
    if n is None:
        return sorted(self.items(), key=_itemgetter(1), reverse=True)
    return _heapq.nlargest(n, self.items(), key=_itemgetter(1))
```

Which is originally using function provided by `_heapq`, which can solve this problem with $$O(Nlogk)$$ time complexity.





```python
from collections import Counter
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        c = Counter(nums)
        t = 0
        for i in range(3):
            t1 = 0
            while t1 < c[i]:
                nums[t] = i
                t1 += 1
                t += 1
        return
```



```python
from collections import Counter
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        i, j, k = 0, 0, len(nums) - 1
        while j <= k and i <= j:
            while nums[j] != 1 and i <= j and j <= k:
                if nums[j] == 0:
                    nums[j], nums[i] = nums[i], nums[j]
                    i += 1
                else:
                    nums[j], nums[k] = nums[k], nums[j]
                    k -= 1
                
            j += 1
            if j > k:
                return
            
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        i, j, k = 0, 0, len(nums) - 1
        while j <= k:
            if nums[j] == 2:
                nums[j], nums[k] != nums[k], nums[j]
                k -= 1
            elif nums[j] == 0:
                nums[i] = 0
                if j > i:
                    nums[j] = 1
                i += 1
                j += 1
            else:
                j += 1

```



## References

1. [`_heapq.nlargest()`](https://hg.python.org/cpython/file/3.6/Lib/heapq.py#l524)
2. [`collections.Counter`](https://github.com/python/cpython/blob/47b7c227048f2cb019cc3ec2fef7e867f1b232f3/Lib/collections/__init__.py#L575-L586)

