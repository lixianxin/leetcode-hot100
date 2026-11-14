# 知识点 W01 · Python 语法基础（第 1 周）

> 用途：第 1 周（11/16–11/20）做题要用到的全部语法。**只学这一页，不要开教程。**
> 关联：第01周-D1至D6-任务卡.md、错题总表.md（预警清单）
> 更新：2026-11-14

---

## 0. 本周只需要会这些

看完这一页大约 30 分钟。看不完就分 5 天看，每天对应 D1–D5 那一节。

**明确不学**：装饰器、类的高级用法、生成器、异步、numpy、pandas、文件读写。刷题用不到。

---

## 1. 变量与类型（D1）

```python
a = 3          # 整数 int
s = "abc"      # 字符串 str
f = 1.5        # 浮点 float
b = True       # 布尔 bool

print(a, s)            # 3 abc
print(f"{a} 和 {s}")   # 3 和 abc     ← f-string，调试时最常用
```

**三种类型转换**

```python
int("3")     # → 3
str(3)       # → "3"
int(3.9)     # → 3    ← 直接截断，不是四舍五入
```

**坑**：`"3" + 3` 会报 `TypeError`。字符串和数字不能直接相加，必须先转。

**判断类型**

```python
type(a)                  # <class 'int'>
isinstance(a, int)       # True
```

---

## 2. 字符串操作（D1、D3、D5）

```python
s = "Hello"
len(s)          # 5
s[0]            # "H"      ← 索引从 0 开始
s[-1]           # "o"      ← -1 表示最后一个
s[1:3]          # "el"     ← 切片，含左不含右
s[:2]           # "He"
s[2:]           # "llo"
s[::-1]         # "olleH"  ← 反转，步长 -1

s.lower()       # "hello"
s.upper()       # "HELLO"
s.split()       # 按空白切分
```

### split 的两个版本（D3 会踩坑）

```python
"  a  b  ".split()      # ["a", "b"]          ← 不带参数：自动合并连续空格、去掉首尾空格
"  a  b  ".split(" ")   # ["", "", "a", "", "b", "", ""]   ← 带参数：一个空格都不放过
```

**结论：刷题时几乎总是用 `s.split()`，不要带参数。** 58 题就是在这里翻车的。

---

## 3. list 与原地修改（D2）

```python
a = [1, 2, 3]
len(a)          # 3
a[0]            # 1
a[-1]           # 3
a[0] = 9        # 改：a 变成 [9, 2, 3]

a.append(4)     # 尾部加：  [9, 2, 3, 4]
a.pop()         # 弹出尾部： 返回 4
a.insert(0, 7)  # 头部插入：[7, 9, 2, 3]   ← 注意这是 O(n)，所有元素要往后挪
```

**遍历的两种写法**

```python
for x in a:                      # 只要值
    print(x)

for i in range(len(a)):          # 要下标（因为要改 a[i]）
    a[i] = a[i] * 2
```

### "原地修改"是什么意思（D2 的核心）

题目说「**原地**删除/修改」，意思是：**不许新建一个数组返回，只能改原来那个数组**。

所以 26 题不能写：

```python
return list(set(nums))        # ✗ 这是新建了一个数组，原数组没动
```

必须写成：

```python
# 在原数组上，把不重复的元素往前挪
k = 1
for i in range(1, len(nums)):
    if nums[i] != nums[k-1]:
        nums[k] = nums[i]
        k += 1
return k                      # ✓ 返回长度 k，nums[:k] 就是结果
```

**为什么返回值是 k 而不是数组**：判题程序会检查 `nums[:k]` 的内容。你返回 k，它自己去看数组前 k 个对不对。

---

## 4. 循环与 range（D3）

```python
range(3)                 # 0, 1, 2          ← 不含 3！
range(1, 4)              # 1, 2, 3          ← 含左不含右
range(0, 5, 2)           # 0, 2, 4          ← 第三个参数是步长
range(4, -1, -1)         # 4, 3, 2, 1, 0    ← 倒序！三步参数缺一不可
```

`range(4, -1, -1)` 是 66 加一题的关键。三个参数分别读作：**从 4 开始，到 -1 之前停（即到 0），每次减 1**。

```python
while i < n:
    ...
    i += 1

for x in a:
    if x == 0:
        break            # 立刻跳出整个循环
    if x < 0:
        continue         # 跳过本次，进入下一轮
```

---

## 5. dict 与 set（D5，全课程最重要的一节）

```python
d = {}                       # 空字典
d["a"] = 1                   # 写入
d["a"]                       # 读取 → 1；键不存在会 KeyError
"a" in d                     # 判断存在 → True
d.get("b", 0)                # 安全读取 → 0（不存在时返回默认值，不报错）
for k, v in d.items():       # 同时遍历键和值
    print(k, v)
```

```python
s = set()                    # 空集合（不能写 s = {}，那是空字典！）
s.add(1)
1 in s                       # True
len(set([1,2,2,3]))          # 3
```

### 这一节唯一必须记住的结论

| 操作 | 数据结构 | 时间复杂度 |
|---|---|---|
| `x in a` | list | **O(n)** ← 从头扫一遍 |
| `x in d` | dict | **O(1)** ← 直接定位 |
| `x in s` | set | **O(1)** |

**为什么重要**：217 题如果你在循环里用 `nums.count(x)`，那是 O(n) 套 O(n) = O(n²)。用 set 就是 O(n)。**后面 90 道题的优化，一大半是在做这个替换。**

### enumerate

```python
for i, x in enumerate(["a", "b"]):
    print(i, x)      # 0 a  /  1 b
```

比 `for i in range(len(a)): a[i]` 更干净，能用就用。

---

## 6. 函数（D4）

```python
def add(a, b):
    return a + b

def find(nums, target):
    for i, x in enumerate(nums):
        if x == target:
            return i
    return -1            # ← 循环里没找到，要有兜底返回
```

**坑**：函数没写 `return` 会返回 `None`，**不会报错**。所以写的时候一定要检查每条路径都有返回。

---

## 7. 本周必背的 15 行

做题时卡住，先来这一节找：

```python
s[::-1]                        # 字符串反转
len(a)                         # 长度
a[-1]                          # 最后一个
range(len(a))                  # 下标遍历
range(n-1, -1, -1)             # 倒序
nums.append(x)                 # 尾部加
nums.insert(0, x)              # 头部插
d.get(k, 0)                    # 安全取值
x in s                         # set 判断（O(1)）
set(a)                         # list 去重
sorted(a)                      # 排序，返回新数组
a.sort()                       # 原地排序，返回 None
s.split()                      # 按空白切分
f"{x}"                         # f-string
isinstance(x, int)             # 类型判断
```

---

## 8. 复杂度怎么数（D5 起每天用）

| 写法 | 复杂度 |
|---|---|
| `a[i]`、`d[x]`、`len(a)` | O(1) |
| `for x in a` | O(n) |
| `x in a`（list） | O(n) |
| `x in d` / `x in s` | O(1) |
| `a.insert(0, x)`、`a.pop(0)` | O(n) |
| `sorted(a)` | O(n log n) |
| 双重 `for` | O(n²) |

**判断口诀**：有几层"跟 n 有关的循环"，就是 n 的几次方。循环里调了一次 O(n) 的函数，层数要加一层。

---

## 9. 本周踩坑对照表

| 症状 | 原因 | 修法 |
|---|---|---|
| `TypeError: can only concatenate str` | 字符串 + 数字 | `int()` 或 `str()` 转换 |
| `IndexError: list index out of range` | 下标超了 | 取元素前先判 `len`，或循环边界改成 `len(a)-1` |
| `KeyError` | 直接 `d[k]` 取不存在的键 | 用 `d.get(k, 默认值)` |
| `AttributeError: 'NoneType'` | 函数漏了 `return` | 检查每条分支都有返回 |
| `s = {}` 想建空集合 | `{}` 是空字典 | 用 `set()` |
| 结果和预期差 1 | `range` 右边界不含 | 记住 `range(a,b)` 是 `a..b-1` |
| 删元素时漏删 | 循环里 `remove` 导致下标移位 | 用写指针双指针法 |
