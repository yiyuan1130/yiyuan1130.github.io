---
layout: post
title:  "Unity C# 扩展类"
date:   2017-11-11 20:03:01 +0800
categories: Unity
---

在编程过程中，很多时候会用到unity/C#中已经给定的方法，但是有时候，这些给定的方法并不能满足我们的需求，在使用给定方法的时候还需要用到别的操作，这些操作往往是固定的。如果每次使用给定的方法时候都手动添加，会造成代码的冗杂，代码量变多，所以此时我们要将给定的方法进行拓展。

**扩展方法的创造：**
扩展方法其实就是编程人员自己写一个方法，这个方法内包含了系统给定的方法，还包含了开发人员自己需要进行的操作，两部分放到一起，就组成了扩展方法。第一个参数用 this 关键字修饰，表示调用此方法的当前对象，其后的参数，按照自己的需求即可。

**例：**
1、UI中我们设置父物体用 SetParent() 方法，通常在设置父物体之后，要将此物体的缩放置1、位置归0；此时我们就可以写一个扩展方法，里面放入这些操作。

```
	/// <summary>
    /// 自动设置父物体
    /// </summary>
    /// <param name="transform"> 操作对象 </param>
    /// <param name="parent"> 要设置的父对象 </param>
    public static void AutoSetParent(this Transform transform, Transform parent)
    {
        transform.SetParent(parent);
        transform.localPosition = Vector3.zero;
        transform.localScale = Vector3.one;
    }
```
**用法：**如果将 Iamge image 对象的父物体设置为TransForm parent 对象，则只需要：

```
image.AutoSetPatent(parent);
```
这样操作之后就实现了设置父物体并且缩放置1，位置归0。



2、如果某个项目需求：List<>中元素改变时候，调用委托，如下代码：

```
 private List<int> handCards;
 public List<int> HandCards
    {
        get
        {
            return handCards;
        }
        set
        {
            handCards = value;
            // 本意为：List<>中数值改变，调用委托
            downPlayerHandCardChangeEvent(); // 已经被定义委托
        }
    }
```

如果List<>进行添加或者移除元素操作(HandCards.Add()/HandCards.Remove())并不会调用委托，因为只改变了里面的元素，并没有改变List<>本身。
此时我们若想调用委托，则需要写一个扩展方法，在添加或者移除元素后，调用一下委托。

```
/// <summary>
    /// List的Add绑定委托扩展方法
    /// </summary>
    /// <param name="list"> 操作List </param>
    /// <param name="addItem"> 要添加的元素 </param>
    /// <param name="eventHandle"> 添加元素后调用的委托 </param>
    public static void AddBandDelegate(this List<int> list, int addItem, OnPlayerhandCardChangeEventHandle eventHandle)
    {
        list.Add(addItem);
        eventHandle();
    }
    
    /// <summary>
    /// List的Remove绑定委托扩展方法
    /// </summary>
    /// <param name="list"> 操作List </param>
    /// <param name="removeItem"> 要移除的元素 </param>
    /// <param name="eventHandle"> 移除元素后调用的委托 </param>
    public static void RemoveBandDelegate(this List<int> list, int removeItem, OnPlayerhandCardChangeEventHandle eventHandle)
    {
        list.Remove(removeItem);
        eventHandle();
    }
```
**用法：**如果想操作List<>添加或者移除元素并且调用委托，只需要如下操作：

```
list.AddBandDelegate(addItem, listAddEvent);
list.RemoveBandDelegate(removeItem, listRemoveEvent);
```

这种调用方法后，就实现了在添加元素或者移除元素的同时，调用相应的委托（**注意：**委托作为参数时候，定义委托这时候不能加 event 关键词），就能实现不改变数组本身，也能调用委托。

**学会使用扩展方法类，让你的代码更简洁，让你的开发更有效率！**