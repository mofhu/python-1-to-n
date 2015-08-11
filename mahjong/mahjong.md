# Mahjong

outline

- 算法
- 函数与参数传递
- 封装的一些考虑 try...except...

## 算法

1. 面向对象实现麻将牌(suit/rank), 面子/雀头
2. 标准型和牌判断: 基于递归的实现
3. 非标准型(七对子 十三幺)和牌判断
4. 封装和听牌判断

### 函数与参数传递

mutable object: 列表的改变

```
>>> x = []
>>> y = x
>>> y.append(10)
>>> y
[10]
>>> x
[10]
```

`y = x` 并不拷贝一个 list, 而是把 y 指向 x 指向的 list(至少是很类似于指针); 另外 list 是可变的, 因此 x y 一起改变
对于 immutable object (numbers, strings, tuples), 对其运算时会产生新的对象.

```
>>> x = 5  # ints are immutable
>>> y = x
>>> x = x + 1  # 5 can't be mutated, we are creating a new object here
>>> x
6
>>> y
5
```

可用 is 或 `id()` 帮助判断 

- is: 判断是否是完全相同的对象; 对象的比较细节参见
- id(): CPython 中返回对象的内存地址