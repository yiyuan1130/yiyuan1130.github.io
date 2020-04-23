---
layout: post
title:  "Lua实现C#类"
date:   2020-04-22 20:20:08 +0800
categories: Lua
---


直接上代码：
### class.lua
```lua
local mt = {}

function class(clsName, base)
	local cls = {}
	base = base or mt
	setmetatable(cls, {__index = base})
	cls.clsName = clsName or "default"
	cls.base = base
	cls.new = function(...)
		local cls_instance = {}
		setmetatable(cls_instance, {__index = cls})
		if cls_instance.onCreate then
			cls_instance:onCreate(...)
		end
		return cls_instance
	end
	return cls
end
```
主要是通过`lua`中原方法实现继承关系，`class`作为一个全局函数，在`class`函数中为声明一个新的类，并声明`new`函数作为模拟C#中 `new XXX(...)`语法，使用方法为`XXX.new(...)`生成对应类的实例。在不同的开发需求中，可以根据需求不同，在`new`函数中实现不同的逻辑，例如上面代码中的`onCreate`就是在声明一个类的实例时候调用的，可以理解为构造方法。另外在具体的开发需求中，默认`mt`也可以根据需求来定制，为类添加默认属性。

### test.lua
``` lua
-- 动物类
local Animal = class("Animal")
Animal.onCreate = function(self, name)
	self.name = "动物->" .. (name or "默认动物")
end
Animal.eat = function(self)
	print("动物(Animal)吃")
end

-- 狗类
local Dog = class("Dog", Animal)
Dog.onCreate = function(self, name)
	self.name = "狗->" .. (name or "无名氏")
end
Dog.eat = function(self)
	print("狗(Dog) 吃")
end
Dog.bark = function(self)
	print("叫(Dog)")
end
Dog.drink = function(self)
	print("喝(Dog)")
end

-- 金毛
local JinMao = class("JinMao", Dog)
JinMao.bark = function(self)
	print("叫(JinMao)")
end

-- 哈士奇
local HaShiQi = class("HaShiQi", Dog)
HaShiQi.drink = function(self)
	print("喝(HaShiQi)")
end

-- 波斯猫
local BoSiMao = class("BoSiMao", Animal)
BoSiMao.bark = function(self)
	print("喵~")
end

print("---------------- 金  毛 ----------------")
local jinMao = JinMao.new("金毛 - 豆豆")
jinMao:eat()
jinMao:bark()
jinMao:drink()

print("---------------- 哈士奇 ----------------")
local haShiQi = HaShiQi.new("哈士奇 - 二哈")
haShiQi:eat()
haShiQi:bark()
haShiQi:drink()


print("---------------- 波斯猫 ----------------")
local boSiMao = BoSiMao.new("波斯猫 - 莎莎")
boSiMao:eat()
boSiMao:bark()

--[[	输出结果
---------------- 金  毛 ----------------
狗(Dog) 吃
叫(JinMao)	狗->金毛 - 豆豆
喝(Dog)
---------------- 哈士奇 ----------------
狗(Dog) 吃
叫(Dog)
喝(HaShiQi)	狗->哈士奇 - 二哈
---------------- 波斯猫 ----------------
动物(Animal)吃
喵~	动物->波斯猫 - 莎莎
[Finished in 0.1s]
]]
```
在测试代码中，继承关系如下：
`HaShiQi/JinMao : Dog : Animal`
`BoSiMao : Animal`
在输出结果中可以看到，`HaShiQi`和`JinMao`具有`Animal`和`Dog`的属性，而`BoSiMao`只有`Animal`的属性。
此文只是提供一个简单的用Lua实现类的大体思路，具体实现方式可以自由扩展