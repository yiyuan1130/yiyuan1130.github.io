---
layout: post
title:  "权重随机算法-Lua实现"
date:   2018-12-26 20:03:01 +0800
categories: Algorithm
---

使用语言：Lua
实现权重随机步骤：
  - 构建一个权重列表，映射所有将数据表对应索引为的数据
  - 计算所有元素的权重总值 sum
  - 从 1-sum 中随机出要比较的值 toCompareWeight
  - sum依次循环减权重表里面的权重值
  - 直到差值小于 toCompareWeight,此时的索引下标就是随机出来的，在原数据表中得到对应索引下标就是随机值

{% highlight ruby %}
--[[
    function:权重随机函数
    param1:要随机的表
    param2:权重列表
    return:返回的随机值
]]
function getRanomByWeight(t, weights)
    local sum = 0
    for i = 1, #weights do
        sum = sum + weights[i]
    end
    local compareWeight = math.random(1, sum)
    local weightIndex = 1
    while sum > 0 do
        sum = sum - weights[weightIndex]
        if sum < compareWeight then
            return t[weightIndex]
        end
        weightIndex = weightIndex + 1
    end
    print("compare error, return nil")
    return nil
end

--------------- test --------------

local weights = {
    [1] = 10,
    [2] = 10,
    [3] = 20,
    [4] = 20,
    [5] = 40,
}

local items = {
    [1] = "A",
    [2] = "B",
    [3] = "C",
    [4] = "D",
    [5] = "E",
}

local result = {
    ["A"] = 0,
    ["B"] = 0,
    ["C"] = 0,
    ["D"] = 0,
    ["E"] = 0,
}
local allRandomCount = 10000000
for i = 1, allRandomCount do
    local randomValue = getRanomByWeight(items, weights)
    result[randomValue] = result[randomValue] + 1
end

for i = 1, #items do
    local key = items[i]
    local count = result[key]
    local probability = count / allRandomCount
    local resultStr = key .. ": " .. "count is " .. count .. " ,probability is " .. probability
    print(resultStr)
end

---------- allRandomCount = 100 ----------
A: count is 14 ,probability is 0.14
B: count is 14 ,probability is 0.14
C: count is 19 ,probability is 0.19
D: count is 19 ,probability is 0.19
E: count is 34 ,probability is 0.34

---------- allRandomCount = 10000 ----------
A: count is 1020 ,probability is 0.102
B: count is 1011 ,probability is 0.1011
C: count is 2049 ,probability is 0.2049
D: count is 1977 ,probability is 0.1977
E: count is 3943 ,probability is 0.3943

---------- allRandomCount = 10000000 ----------
A: count is 1001325 ,probability is 0.1001325
B: count is 1000326 ,probability is 0.1000326
C: count is 1999609 ,probability is 0.1999609
D: count is 1998877 ,probability is 0.1998877
E: count is 3999863 ,probability is 0.3999863
{% endhighlight %}

从测试数据来看，随机次数越多，越接近单个权重值占总权重值的比例