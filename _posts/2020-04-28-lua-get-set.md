---
layout: post
title:  "Lua实现C#的get\set"
date:   2020-04-22 20:20:08 +0800
categories: Lua
---

虽然Lua没有C#一样的属性，但是它的强大的元表和元方法能够实现很多功能，本篇博客就介绍如何用lua的元表和原方法实现C#中的get、set属性访问其功能。

### class.lua
class.lua是实现lua的类，我的之前博客有些过简易版的，属性访问的的实现相当于在最简易版的基础上进行扩展，博客链接：[Lua 实现C#中的类](https://yiyuan1130.github.io/lua/2020/04/22/lua-c-class.html)

```c
local mt = {}

function class(clsName, base)
	local cls = {}
	base = base or mt
	cls.__get__ = {}
	cls.__set__ = {}
	setmetatable(cls, {__index = base})
	cls.clsName = clsName or "default"
	cls.base = base
	cls.new = function(...)
		local cls_instance = {}
		cls_instance.getset_values = {}
		for k,v in pairs(cls) do
			cls_instance[k] = v
		end
		local cls_instance_mt = {
			__index = function(t, k)
				if cls[k] then
					return cls[k]
				end
				if t.__get__[k] then
					t.__get__[k](t)
					return t.getset_values[k]
				end
				if string.sub(k, 1, 2) == "__" then
					local tmpK = string.sub(k, 3, -1)
					if t.getset_values[tmpK] then
						return t.getset_values[tmpK]
					end
				end
			end,
			__newindex = function(t, k, v)
				if t.__set__[k] then
					t.__set__[k](t, v)
					cls_instance.getset_values[k] = v
					return
				end
				if string.sub(k, 1, 2) == "__" then
					local tmpK = string.sub(k, 3, -1)
					if t.getset_values[tmpK] then
						t.getset_values[tmpK] = v
					end
				end
				rawset(t, k, v)
			end
		}
		setmetatable(cls_instance, cls_instance_mt)
		if cls_instance.onCreate then
			cls_instance:onCreate(...)
		end
		return cls_instance
	end
	return cls
end
```
重要扩展方式是给`cls_instance`添加了一个`metatable`，这个`metatable`功能有以下几方面：
1. 检查`xxx`字段是否是`__get__`和`__set__`的内部字段（`__get__.xxx`）
2. 调用`__get__` `__set__` 对应的函数
3. 修改`getset_values `中`xxx`的值
4. 判断`__xxx`（在实例中`__xxx`作为属性的真实存储值，如果不想通过`get`或者`set`获取，直接`instance.__xxx`就不会出发对应函数）
5. 将`_xxx`转为`xxx`去`getset_values`中找值并返回

#### test.lua
分割线之上是声明一个类，分割线下是测试的代码，输出结果分别对应着`p1.age = 10`到`print(p1.__age)`的每行输出
```c
local Person = class("Person")
Person.onCreate = function(self, name)
	self.name = name
end
Person.__get__.age = function(self)
	print(self.name, " - 属性访问器：get age")
end
Person.__set__.age = function(self, value)
	print(self.name, " - 属性访问器：set age", value)
end
-----------------------------------
local p1 = Person.new("张三")
p1.age = 10
local tmpAge = p1.age
p1.age = p1.__age + 1
print(p1.__age)

--[[
	张三	 - 属性访问器：set age	10
	张三	 - 属性访问器：get age
	张三	 - 属性访问器：set age	11
	11
]]
```
