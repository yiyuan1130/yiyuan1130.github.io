---
layout: post
title:  "随机取n个不相同的元素（Lua）"
date:   2018-12-20 20:03:01 +0800
categories: Algorithm
---

### 随机取n个不相同的元素（Lua实现）
一些逻辑逻辑需求要在一个长度为m数组中取出n个不同的元素（m > n），一般第一想法是随机出一个下标，然后删除对应的元素，以此来取出不同的元素。本篇文章介绍一个不需要删除操作的方法。代码如下：
``` Lua
-- 假设长度m为10，随机取出个数为n=3
local table = {1, 2, 3, 4, 5, 6, 7, 8, 9, 0}
local count = 3
local length = #table
for i = 1, count do
    local ri = math.random(i, length)
    local tmp = table[i]
    table[i] = table[ri]
    table[ri] = tmp
end
-- table中前3个元素就是要取出的三个元素
```
实现思路：第一次循环在1-m中随机一个下标ri，然后将下标1和ri交换，第二次循环在2-m中随机一个下标ri，将ri和2进行交换，最后table中前三个元素就是随机出来的元素。