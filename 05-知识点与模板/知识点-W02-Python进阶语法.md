# 知识点 · W02 Python 进阶语法（第 2 周）

> 每天只读当天对应的一节，约 25–30 分钟。读完自检题能口头答出，才算读完。  
> 关联：`第02周-D1至D6-任务卡.md`、`模板库-代码骨架.md`、`知识点-W01`（第 10 节是上周攻坚沉淀，先复习）

---

## 1. set 与 tuple、集合运算（D1 · 11/24）

### 1.1 set 是什么

**无序、不重复**的元素集合。只回答「某个值**在不在**」，永远不回答「在**第几个**位置」——这是 014 两次翻车的根源，现在把它变成正面武器：

```python
s = set()
s = {1, 2, 3}            # 字面量；注意 {} 是空 dict，空集合必须写 set()
s.add(4)                 # 加元素
s.remove(4)              # 删元素（不存在会 KeyError）
len(s)                   # 元素个数
x in s                   # 平均 O(1)——这是 set 最大的价值
list(s)                  # 转回列表（顺序不可靠！）
set(nums)                # 从 list 一键去重
```

### 1.2 集合运算（三个符号）

```python
a & b     # 交集：两边都有的
a | b     # 并集：合起来去重
a - b     # 差集：a 有 b 没有的（方向别反！）
```

**方向口诀**：`a - b` 是「从 a 里去掉 b 里也有的」。268 丢失的数字 = `完整集合 - 现有集合`，别写反。

**Counter 也有 `&`**，但语义不同：是「多重集合交集」——每个字符取两边较小的次数（D4 的 383 会用）。set 的 `&` 只管在不在，Counter 的 `&` 管数量。

### 1.3 tuple（了解即可）

不可变的「列表」：`t = (1, 2)`。刷题主要用它两处：字典的 key 可以是 tuple（list 不行），`enumerate` 返回的就是 (下标， 元素) 元组。

### 1.4 今天两题的模型预告（做题前只看模型名，不看骨架）

- **268**：`完整集合` 与 `现有集合` 的差集。模型：set 差集
- **448**：先把 1..n 的完整集合建出来，再差集，再转列表返回。模型：set 差集（268 的多输出版）

### 自检（口头答）

1. `{}` 是什么？空集合怎么写？
2. `a - b` 和 `b - a` 一样吗？
3. set 能不能做字典的 key？tuple 呢？为什么？

---

## 2. sorted 与切片进阶（D2 · 11/25）

### 2.1 sorted vs sort

```python
a.sort(key=..., reverse=False)   # 原地改，返回 None！b = a.sort() 是错的
b = sorted(a, key=..., reverse=True)  # 不动 a，返回新列表
```

`key` 是**函数**：`sorted(words, key=len)` 按长度排；`key=lambda x: x[1]` 按元组第二位排。

### 2.2 切片全家桶 `a[start:stop:step]·`

| 写法        | 含义         |
| --------- | ---------- |
| `a[::-1]` | 反转（W01 已用） |
| `a[::2]`  | 隔一个取一个     |
| `a[-3:]`  | 最后 3 个     |
| `a[:-1]`  | 除最后一个      |

**a.sort() 返回 None** 是本周最容易踩的坑（W01 的 `is None` 教训同源：函数返回值不一定是你想的东西）。

---

## 3. 列表推导式与字符串处理（D3 · 11/26）

```python
[x*2 for x in a if x > 0]              # 一层：过滤+变换
[[i, c] for i, c in enumerate(s)]      # 解包写法
"".join(ch.lower() for ch in s if ch.isalnum())   # 过滤+小写+拼接，125 用
```

**`"".join(...)` 的元素必须全是 str**——W01 已讲，`map(str, digits)` 是救援通道。

**125 的新知识**：`ch.isalnum()`（字母或数字都算 True）、`ch.lower()`。空串经 join 后是 `""`——**"" 判回文是 True**（题面定义，别自作主张特判）。

---

## 4. Counter / defaultdict / 手写计数（D4 · 11/27）

```python
from collections import Counter, defaultdict

c = Counter("aabbc")        # {'a':2, 'b':2, 'c':1}
c.most_common(1)            # [(出现最多的元素, 次数)]
c["z"]                      # 不存在返回 0，不报错（普通 dict 会 KeyError）
c1 == c2                    # 整体相等比较（W01 242 的教训）
Counter(a) & Counter(b)     # 多重集合交集：每个元素取 min 次数

d = defaultdict(int)        # 访问不存在的 key 自动建 0
d[x] += 1                   # 计数不用先判断
d2 = defaultdict(list)      # 自动建空列表，分组利器（W03 字母异位词分组就靠它）
```

**手写计数仍是必会版**（`d[k] = d.get(k, 0) + 1`），Counter 是版②——面试手写更稳，但你要知道 Counter 存在且能读。

---

## 5. class 与 self、复杂度自算（D5 · 11/28）

### 5.1 缩进 = 作用域（014 翻车点正式总结）

```python
class Solution:
    def search(self, nums, target):      # 类方法：缩进 1 层
        return self.helper(nums, 0, len(nums)-1, target)

    def helper(self, nums, l, r, t):     # 和上面平级（都缩进 1 层）才是类方法
        ...
        # 如果 def 写到 search 里面（缩进 2 层），它只是局部函数，
        # self.helper 找不到 → AttributeError
```

**类内互相调用必须带 `self.`**。

### 5.2 二分查找模板（704，必背）

```python
l, r = 0, len(nums) - 1
while l <= r:                # 带等号：单元素区间也要进
    mid = (l + r) // 2       # Python 无溢出问题；Java 要写 l+(r-l)/2，会讲就行
    if nums[mid] == target: return mid
    elif nums[mid] < target: l = mid + 1
    else: r = mid - 1
return -1
```

**`<=` 少等号 = 单元素区间漏判**。为什么 `mid+1 / mid-1` 而不是 `mid`：mid 已查过，区间必须收缩，否则死循环。

### 5.3 复杂度口算（W01 结论复述）

- **先看题面约束再报复杂度**（242 的 26 key → O(1)）
- 嵌套层数 = n 的几次方；循环里调 O(n) 函数要加层
- set/dict 增删查平均 O(1)（期望，不是无条件最坏——W03 细讲）
