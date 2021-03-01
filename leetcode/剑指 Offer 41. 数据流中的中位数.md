对于这道题，我真的是服了，心服口服的服。

首先我考虑到的是数组或者链表，一直保持有序，然后利用插入或者二分法快速找到插入位置。但是这里都会有问题：

1. 数组二分法可用，查找效率高，但是插入效率低
2. 链表插入效率高，但是由于不支持随机读取，只能顺序查找插入位置

后来也想到了大顶堆，但是想一个大顶堆虽然查找和插入都很方便，但是遇到输出中位数的时候，需要做一次转换，效率也不高。

直到我看到了[高人的解法](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/solution/mian-shi-ti-41-shu-ju-liu-zhong-de-zhong-wei-shu-y/)，
用两个堆，一个小顶堆存放较大的一部分，堆顶是大组的最小数；一个大顶堆存放较小的一部分，对顶是小组的最大数。

这样既保留了堆插入和查找快速的优点，同时可以利用两个堆的对顶快速求得中位数。

**太棒了！**

而且python还提供了小顶堆的操作方法，非常银杏。果然是人生苦短快用python。

```python3
from heapq import *

class MedianFinder:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.big_heap = []      #小顶堆，保存较大的一半，保存正数
        self.small_heap = []    #大顶堆，保存较小的一半，保存负数


    def addNum(self, num: int) -> None:
        if len(self.big_heap) != len(self.small_heap):
            heappush(self.big_heap, num)
            heappush(self.small_heap, -heappop(self.big_heap))
        else:
            heappush(self.small_heap, -num)
            heappush(self.big_heap, -heappop(self.small_heap))

    def findMedian(self) -> float:
        if len(self.big_heap) == len(self.small_heap):
            return (self.big_heap[0] - self.small_heap[0])/2
        else:
            return self.big_heap[0]

```

