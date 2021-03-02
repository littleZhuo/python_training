python中是不支持`switch-case`语句的，之前不是没人提过，只是被吉多给否了，具体可以看这个 [PEP 3103](https://www.python.org/dev/peps/pep-3103/)。
> PEP 3103 -- A Switch/Case Statement



那么如何在python中实现类似的功能？

---

## 方案1，`if-elif-else`

这应该是能想到的最简单的方案

```python
def switch_case_by_if(a):
    if a == 1:
        return 100
    elif a == 2:
        return 200
    else:
        return 0
```

## 方案2，`dict`

这个方案就比较`pythonic`，需要注意的是，dict的value需要是函数定义或者迭代器这种lazy的东西，如果直接放表达式，那么在每次调用的时候都会被执行。

```python
def switch_case_by_dict(a):
    return {
        1: lambda : 100,
        2: lambda : 200
    }.get(a, lambda : 0)()
```

## 方案3，`singledispatch`

咨询了公司python群里的大佬，给指了一条`singledispatch`的路，第一次用，折腾了半天。很有意思，但是发现要多写一堆的前置代码，用在这里有一种大炮打蚊子的感觉。

```python
from functools import singledispatch
from collections import namedtuple

ONE = namedtuple('ONE', [])
TWO = namedtuple('TWO', [])
OTHER = namedtuple('OTHER', [])
trans_map = {1: ONE, 2: TWO}

def switch_case_by_singledispatch(a):
    @singledispatch
    def switch_case(a, verbose=False):
        return 0

    @switch_case.register
    def _(a: ONE):
        return 100

    @switch_case.register
    def _(a: TWO):
        return 200

    new_type = trans_map.get(a, OTHER)()
    return switch_case(new_type)
```

需要注意的是，因为这里判别的是同类型的1/2/3，所以会麻烦一些，要用`nametuple`来封装新类型。如果是不同的类型的话，能少写不少代码。比如2-7行和22行就不需要了。

## 测试效果

```python
if __name__ == '__main__':
    for i in range(1, 4):
        print(switch_case_by_if(i), switch_case_by_dict(i), switch_case_by_singledispatch(i))
        
'''
输出：
100 100 100
200 200 200
0 0 0
'''
```

效率上，应该是dict最快，if次之，singledispatch最慢。但是没有实际测试，瞎说不负责。
