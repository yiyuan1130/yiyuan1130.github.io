---
layout: post
title:  "Unity UI EventTrigger 动态添加UI事件"
date:   2018-01-31 16:22:38 +0800
categories: Unity
---

UI中点击、按下、抬起、进入、退出、拖拽等事件，可以引用各自的接口，实现接口中的方法来完成相应需求。但是如果一个对象身上要完成很多事件，引用大量接口就显得麻烦了。为了避免引用借口过多，实现动态绑定事件可以用EventTrigger组件来完成。下面给大家演示一下EventTrigger组件的使用方法，以及如何在代码里动态添加所需的事件。

### EventTrigger的在Inspector中使用
EventTrigger在Inspector面板的属性：在代码中写好对应事件的方法，选出要添加Event Type，像Button一样把方法拖拽到对应的EventType上即可。

### EventTrigger的在带代码动态绑定事件使用
代码中动态创建要有以下几个步骤：
1. 实例化所有委托的列表
2. 新建所需的事件
3. 注册eventID
4. 新建callback
5. 设置对应事件的内容
6. 绑定事件
7. 把事件添加到委托列表

以上7步是EventTrigger动态添加事件的步骤，按照步骤来就很容易了。

EventTrigger用好动态添加事件，会很方便，省去很多接口，随便一个UI控件加上EventTrigger组件后，所有的交互都可以很容易的完成。下面附上代码：

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.Events;
using UnityEngine.EventSystems;

public class EventTriggerTest : MonoBehaviour {

    Text testT;
    EventTrigger testET;
    private void Start()
    {
        testT = transform.Find("Text").GetComponent<Text>();
        
        testET = gameObject.GetComponent<EventTrigger>();
        if (testET == null)
            testET = gameObject.AddComponent<EventTrigger>();

        // 实例化委托列表
        testET.triggers = new List<EventTrigger.Entry>();

        // 注册事件
        EventTrigger.Entry entryPointerEnter = new EventTrigger.Entry();
        EventTrigger.Entry entryPointerExit = new EventTrigger.Entry();
        EventTrigger.Entry entryPointerDown = new EventTrigger.Entry();
        EventTrigger.Entry entryPointerUp = new EventTrigger.Entry();
        EventTrigger.Entry entryDrag = new EventTrigger.Entry();

        // 实例化eventID
        entryPointerEnter.eventID = EventTriggerType.PointerEnter;
        entryPointerExit.eventID = EventTriggerType.PointerExit;
        entryPointerDown.eventID = EventTriggerType.PointerDown;
        entryPointerUp.eventID = EventTriggerType.PointerUp;
        entryDrag.eventID = EventTriggerType.Drag;

        // 实例化callback
        entryPointerEnter.callback = new EventTrigger.TriggerEvent();
        entryPointerExit.callback = new EventTrigger.TriggerEvent();
        entryPointerDown.callback = new EventTrigger.TriggerEvent();
        entryPointerUp.callback = new EventTrigger.TriggerEvent();
        entryDrag.callback = new EventTrigger.TriggerEvent();

        // 设置事件
        UnityAction<BaseEventData> pointerEnterCB = new UnityAction<BaseEventData>(OnPointerEnterCBTarget);
        UnityAction<BaseEventData> pointerExitCB = new UnityAction<BaseEventData>(OnPointerExitCBTarget);
        UnityAction<BaseEventData> pointerDownCB = new UnityAction<BaseEventData>(OnPointerDownCBTarget);
        UnityAction<BaseEventData> pointerUpCB = new UnityAction<BaseEventData>(OnPointerUpCBTarget);
        UnityAction<BaseEventData> DragCB = new UnityAction<BaseEventData>(OnDragCBTarget);

        // 绑定事件
        entryPointerEnter.callback.AddListener(pointerEnterCB);
        entryPointerExit.callback.AddListener(pointerExitCB);
        entryPointerDown.callback.AddListener(pointerDownCB);
        entryPointerUp.callback.AddListener(pointerUpCB);
        entryDrag.callback.AddListener(DragCB);

        // 添加到委托列表
        testET.triggers.Add(entryPointerEnter);
        testET.triggers.Add(entryPointerExit);
        testET.triggers.Add(entryPointerDown);
        testET.triggers.Add(entryPointerUp);
        testET.triggers.Add(entryDrag);
    }
    // 进入 要做的事
    void OnPointerEnterCBTarget(BaseEventData baseEventData)
    {
        testT.text = "PointerEnter";
        Debug.Log("write here when PointerDown");
    }
    // 离开 要做的事
    void OnPointerExitCBTarget(BaseEventData baseEventData)
    {
        testT.text = "PointerExit";
        Debug.Log("write here when PointerExit");
    }
    // 按下 要做的事
    void OnPointerDownCBTarget(BaseEventData baseEventData)
    {
        testT.text = "PointerDown";
        Debug.Log("write here when PointerDown");
    }
    // 抬起 要做的事
    void OnPointerUpCBTarget(BaseEventData baseEventData)
    {
        testT.text = "PointerUp";
        Debug.Log("write here when PointerUp");
    }
    // 点击 要做的事
    void OnDragCBTarget(BaseEventData baseEventData)
    {
        transform.position = Input.mousePosition;
        testT.text = "Drag:" + Input.mousePosition;
        Debug.Log("write here when PointerClick");
    }
}
```