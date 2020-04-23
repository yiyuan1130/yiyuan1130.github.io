---
layout: post
title:  "Lua实现消息模式"
date:   2020-04-22 20:20:08 +0800
categories: Design Mode
---

消息模式有三个主要对象，分别是：
1. 消息监听者：监听消息
2. 消息中心：存储、删除消息
3. 消息发送者：发送广播
看下面的图，大概对每个部分的职能能够清楚的了解：
![在这里插入图片描述](/styles/images/msg_designmode/msg.png)


下面是主要代码：
### Msg.lua
相当于消息中心，对所有消息进行处理
其中Remove的三个参数支持多种方式的移除消息方式，具体的可以参考代码逻辑进行理解和修改
```lua
Msg = {}

Msg.init = function()
	--[[
		msgmap = {
			msgId = {
				{instance1, func1},
				{instance2, func2}
			}
		}
	]]
	Msg.msgmap = {}
end

--@desc 添加绑定事件
--@arg  ... msgId, instance, func
--@arg  msgId 消息Id
--@arg  func 消息执行函数
Msg.Add = function(instance, msgId, func)
	local msgObj = {instance, func}
	if not Msg.msgmap[msgId] then
		Msg.msgmap[msgId] = {msgObj}
	else
		local list = Msg.msgmap[msgId]
		for _, obj in pairs(list) do
			if obj[1] == instance and obj[2] == func then
				return
			end
		end
		table.insert(Msg.msgmap[msgId], msgObj)
	end
end

--@desc 移除绑定事件
--@arg  msgId 消息Id
--@arg  instance 监听实例
--@arg  func 消息执行函数
Msg.Remove = function(msgId, instance, func)
	if msgId then
		if not instance and not func then
			Msg.msgmap[msgId] = nil
		else
			local list = Msg.msgmap[msgId]
			Msg._remove(list, instance, func)
		end
	else
		for id, list in pairs(Msg.msgmap) do
			Msg._remove(list, instance, func)
		end
	end
end
Msg._remove = function(list, instance, func)
	if not list then
		return
	end
	for index, msgObj in pairs(list) do
		if instance and func then
			if msgObj[1] == instance and msgObj[2] == func then
				table.remove(list, index)
			end
		elseif instance and not func then
			if msgObj[1] == instance then
				table.remove(list, index)
			end
		elseif not instance and func then
			if msgObj[2] == func then
				table.remove(list, index)
			end
		end
	end
end

--@desc 发送事件
--@arg  msgId 消息Id息
Msg.Send = function(msgId, ...)
	local list = Msg.msgmap[msgId]
	if list then
		for _, msgObj in pairs(list) do
			if msgObj[2] then
				msgObj[2](...)
			end
		end
	end
end

Msg.init()
return Msg
```

### MsgId.lua
用于存储消息Id的脚本
```lua
MsgId = {}

MsgId.DOG_RUN = "DOG_RUN"

-- 简单实现个只读功能
setmetatable(MsgId, {__newindex = function()
	print("new id must add in MsgId.lua")
end})
```

### test.lua
其中的dog1，dog2，dog3，相当于消息监听者，通过```Msg.Add```函数监听消息；```Msg.Send```相当消息发送者，广播对应的消息id
```lua
--[[
	class 函数是自己实现的一个声明类的函数
]]
local Dog = class("Dog", nil)

local dog1 = Dog.new()
local dog2 = Dog.new()
local dog3 = Dog.new()
dog3.AddMsg = function(self)
	Msg.Add(self, MsgId.DOG_RUN, function(...)
		print("dog3 跑, 通过外部添加监听", ...)
	end)	
end

Msg.Add(dog1, MsgId.DOG_RUN, function(...)
	print("dog1 跑, 通过外部添加监听", ...)
end)
Msg.Add(dog2, MsgId.DOG_RUN, function(...)
	print("dog2 跑, 通过外部添加监听", ...)
end)
dog3:AddMsg()

Msg.Send(MsgId.DOG_RUN, "-> test 事件参数")
print("------------------------------------------")
Msg.Remove(MsgId.DOG_RUN, dog2)
Msg.Send(MsgId.DOG_RUN, "-> test 事件参数")
print("------------------------------------------")

--[[ 输出信息：
dog1 跑, 通过外部添加监听	-> test 事件参数
dog2 跑, 通过外部添加监听	-> test 事件参数
dog3 跑, 通过外部添加监听	-> test 事件参数
------------------------------------------
dog1 跑, 通过外部添加监听	-> test 事件参数
dog3 跑, 通过外部添加监听	-> test 事件参数
------------------------------------------
[Finished in 0.1s]
]]
```
在test脚本中：
1. 3个监听者分别监听了`MsgId.DOG_RUN`消息
2. `Msg.Send(MsgId.DOG_RUN, "-> test 事件参数)` 进行了一次广播
3. `Msg.Remove(MsgId.DOG_RUN, dog2)`将消息中心`dog2`的`MsgId.DOG_RUN`消息移除
4. `Msg.Send(MsgId.DOG_RUN, "-> test 事件参数)` 又进行了一次广播，因为之前有移除过dog2的对应消息，所以再次输出中没有dog2的输出。