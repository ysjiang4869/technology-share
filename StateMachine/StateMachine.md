---
theme: gaia
_class: lead
paginate: true
backgroundColor: #fff
backgroundImage: url('https://marp.app/assets/hero-background.jpg')
marp: true
---

![bg left:40% 80%](https://marp.app/assets/marp.svg)

# **状态机分享**

姜岳松

https://marp.app/

---

# 一 背景
其实无论生活、工作中很多地方都应用到了状态机
- 在网站购物的订单：订单从下单到支付，发货，收获，成功等流程
- 游戏的设计：例如挂机自动打怪、自动托管
- 空调等电器：在不同状态下，按钮出现不同的响应
- web前端的UI控制  
......

以上有复杂的场景，也有简单的场景。
在工程应用领域，为了方便的进行建模描述，产生了状态机的概念。

---
# 二 概念
## 2.1 状态机模型的概念
`有限状态机`（英语：finite-state machine，缩写：`FSM`）又称`有限状态自动机`，简称`状态机`，是表示有限个状态以及在这些状态之间的转移和动作等行为的数学模型。
![bg right:40% 80%](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/8c2cfb5d-156e-4ee2-8841-0d2842444ef9/00dbb097-f9fe-4f7f-a63f-92d199603b84.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210424%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210424T031403Z&X-Amz-Expires=86400&X-Amz-Signature=b653dd097835c9b8b6cbf0f7797681b7f3f66d6129a4c2ac9090d8ac445b301d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%2200dbb097-f9fe-4f7f-a63f-92d199603b84.png%22)

---

## 2.2 组成要素

- **现态**：是指当前所处的状态。
- **条件**：又称为`事件`。当一个条件被满足，可能将会触发一个动作，或者执行一次状态的`迁移`。
- **动作**：条件满足后执行的`动作`行为。动作执行完毕后，可以迁移到新的状态，也可以仍旧保持原状态。动作不是必需的，当条件满足后，也可以不执行任何动作，直接迁移到新状态。
- **次态**：条件满足后要迁往的新状态。“次态”是相对于 “现态” 而言的，“次态”一旦被激活，就转变成新的 “现态” 了。

---
## 2.3 三个特征

- 状态总数（`state`）是有限的。
- 任一时刻，**只处在一种状态之中**。
- 某种条件下，会从一种状态转变（`transition`）到另一种状态。

---

## 2.4 类型

### 1. 识别器
  产生一个二元输出，“是” 或 “否”，来回答输入是否被机器接受。
### 2. 变换器
- 摩尔型有限状态机（Moore机）
  输出只依赖于当前状态。例如遥控器的开关机按钮
- 米利型有限状态机（Mealy机）
  输出依赖于当前状态和输入。例如订单中，待支付的订单，输入关闭，次态是已关闭；输入支付，状态是支付完成待发货。

---
# 三 实现方式
看下面一个案例
1. 假设遥控器只有两个按钮，power 电源键和 cool 制冷键。
2. 空调的运行呈现 3 个状态，停止 / Off、仅送风 / FanOnly、制冷 / Cool。
3. 起始状态为 Off 。

![bg right:50% 80%](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b8869a6b-1694-48f9-8941-89126bf40a6b/44230e33-5f35-447c-a4ea-bafb897e165e.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210424%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210424T040224Z&X-Amz-Expires=86400&X-Amz-Signature=9e91dc012c9cc27e5fbc4ec87c25b437d4e41fa5dc69a4d408b7ad637e696a8a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%2244230e33-5f35-447c-a4ea-bafb897e165e.png%22)

---
## 3.1 if-else 或者switch

```java
public void dispather(Event event) {
    if (currentState == OFF) {
        if(event == CLICK_POWER){
            setCurrentState(FAN_ONLY);
            doStartFan();
        }
    } else if (currentState == FAN_ONLY) {
        if(event == CLICK_POWER){
            setCurrentState(OFF);
            doStopFan();
        } else if (event == CLICK_COOL) {
            setCurrentState(COOL);
            doStartCool();
        }
    } else if(currentState == COOL){
        if(event == CLICK_POWER){
            setCurrentState(OFF);
            doStopCool();
        } else if (event == CLICK_COOL) {
            setCurrentState(FAN_ONLY);
            doStartFan();
        }
    }
}
```

---
```java
public void dispather(Event event) {
    switch (currentState) {
        case OFF:
            switch (event) {
                case CLICK_POWER:
                    setCurrentState(FAN_ONLY);
                    doStartFan();
                    break;
            }
            break;
        case FAN_ONLY:
            switch (event) {
                case CLICK_POWER:
                    setCurrentState(OFF);
                    doStopFan();
                    break;
                case CLICK_COOL:
                    setCurrentState(COOL);
                    doStartCool();
                    break;
            }
            break;
        case COOL:
            switch (event) {
                case CLICK_POWER:
                    setCurrentState(OFF);
                    doStopCool();
                    break;
                case CLICK_COOL:
                    setCurrentState(FAN_ONLY);
                    doStartFan();
                    break;
            }
            break;
    }
}
```
---

### 3.2 状态迁移表法

![80%](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1056612b-2f05-4187-b3ea-295b4671b109/01104454-44df-4b75-b605-68547df56f0b.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210424%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210424T041339Z&X-Amz-Expires=86400&X-Amz-Signature=24874ab73f52e61f7fb59417357590069b78e17ca22260397da74d99e723622a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%2201104454-44df-4b75-b605-68547df56f0b.png%22)

---
```java
package com.mhc.sample;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

import static com.mhc.sample.AirconTable.Event.*;
import static com.mhc.sample.AirconTable.State.*;

/**
 * 空调 － 状态转移表模式
 *
 * @author xiaolong
 * @date 18/6/11 下午5:54
 */
public class AirconTable {
    /**
     * 状态转移表
     */
    private List<Transfor> transforTable = new ArrayList<Transfor>() {
        private static final long serialVersionUID = 2679742264102211454L;
        {
            add(Transfor.of( OFF,      CLICK_POWER,  FAN_ONLY, () -> doStartFan() ));
            add(Transfor.of( FAN_ONLY, CLICK_POWER,  OFF,      () -> doStopFan()  ));
            add(Transfor.of( FAN_ONLY, CLICK_COOL,   COOL,     () -> doStartCool()));
            add(Transfor.of( COOL,     CLICK_POWER,  OFF,      () -> doStopCool() ));
            add(Transfor.of( COOL,     CLICK_COOL,   FAN_ONLY, () -> doStartFan() ));
        }
    };
    static class Transfor {
        //开始状态
        State startState;
        //事件
        Event event;
        //目标状态
        State nextState;
        //执行动作
        Runnable doAction;

        static Transfor of(State startState, Event event, State nextState, Runnable doAction) {
            Transfor transfor = new Transfor();
            transfor.startState = startState;
            transfor.nextState = nextState;
            transfor.event = event;
            transfor.doAction = doAction;
            return transfor;
        }
    }
}
```

---

### 3.3 状态设计模式

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/0eb96682-4bb0-4dcc-a5bc-ab8f4bef9705/7ad1ecbc-3159-4e3a-a6f0-be7241b3a2d1.jpeg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210424%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210424T041903Z&X-Amz-Expires=86400&X-Amz-Signature=658b80e98a556cb93a336dda4d1d4ede9ab66cf98e588bd1bb5e7c4752c4663b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%227ad1ecbc-3159-4e3a-a6f0-be7241b3a2d1.jpeg%22)

具体代码省略，在具体的状态代码中，通过switch或者if语句区分具体的输入执行的动作。

---

### 3.4 状态机框架

1. spring-state-machine
  对状态机的概念支持很全面，但是缺点是：状态机必须从初始状态启动。这样状态机在使用中，必须在ThreadLocal中缓存当前的状态，且需要持久化这个状态，需要额外的存储信息。
2. squirrel-foundation
  对状态机的概念支持相对比较全面；可以从任意状态启动；代码也比较轻量、清晰，容易二次开发。
  缺点：没有天生的集成spring，不能很好的利用spring的自动注入功能。
---
3. stateless4j
  没有研究，看介绍状态模型比较简单，对状态机概念的支持不够，不适合用在大型项目中。

---
### 3.5 状态机中的详细概念

### State状态

一个状态模型中不变的条件。状态是一个状态机的主要实体，状态改变是由事件驱动的。

- hierarchical state 分层状态，具层级关系的状态。其中嵌套在其他状态下的状态称为「子状态」。
- 组合状态：状态之中还会嵌套状态，这种被嵌套的状态被称为子状态，而嵌套子状态的状态称为组合状态。也即**具有一个区域的状态。**
- 正交状态：具有两个区域的状态

---
spring state machine还提供以下的伪状态，来实现特定的功能。所谓伪状态，并不是一个状态机中流转的状态，有的是给状态机的状态添加一个标记，有的是扩展一些功能。

- `Initial State`：状态机启动的特殊状态。初始状态总是绑定到特定的状态机或区域。具有多个区域的状态机可能具有多个初始状态。
- `End State`：最终状态是一种特殊的状态，表示封闭区域已完成。如果封闭区域直接包含在状态机中，并且状态机中的所有其他区域也都完成了，则表示整个状态机已完成。
- `History State`：一种允许状态机记住其最后活动状态的伪状态。存在两种类型的历史状态，浅层仅记住顶层状态，深层记录子机中的活动状态。

---
- `Choice State`：允许基于事件标题或扩展状态变量进行转换选择的伪状态。即：标记一个状态有多个分支，可根据不同条件进入不同的target state
- `Junction State`：伪状态，相对`Choice State`而言，支持多个incoming transitions，但是choice state只支持一个
- `Fork/Join State`：一种伪状态，可以控制进入/退出某个区域。下图中的两条灰色的竖线分别代表来fork和join的伪状态

    下图中，State1就是一个正交状态，包含两个正交区域A和B

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e2a786b6-d3d5-4c2c-889c-29cab56053b1/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e2a786b6-d3d5-4c2c-889c-29cab56053b1/Untitled.png)

---

- `Extended State`：保存在状态机中的一个变量的集合，以减少状态机所需的状态数量。（在一个状态中保存一个k-v的map，存储额外的信息）
- `entry/exit point`：伪状态，控制进入或者退出子状态机

---
### Transition转换

代表了状态机中两个状态之间的关系，由事件驱动其转移，状态由A切换为B。有三种类型

- external：外部的转换，「外」是相当于 source state 说的，一次转换的 target state 和 source target 不一样，状态流转到新的状态，其中历经了 exit 和 entry。
- internal：内部的转换，「内」是相当于 source state 说的，Internal transition 的 source state 和 target state 一样，是不会经过 exit 和 entry，主要用于在状态不变的情况下执行响应一些事件。
- local：状态和子状态的相互切换，不会触发source state的extry和exit

---

### Region区域

定义为：**区域是组合状态或状态机的正交部分**，它包含状态和转换。

并发状态：对相同实体的多种同时的视角

正交区：多种并发状态的合成。例如人的状态，从年龄角度可以有儿童、成人，老人；从性别分可以有男、女，那么一个人可以同时符合这两种状态，这两种描述的状态合并到一起，构成两个正交区域。

在一个正交区域内，一个对象同一时刻只能处于一个状态。

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bceb3592-6bc2-4ab4-9d1c-5ae5d22b7831/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bceb3592-6bc2-4ab4-9d1c-5ae5d22b7831/Untitled.png)

---
### Guard

守卫，相当于一种拦截，在某一个操作前设置一个 guard，用 guard 来校验到底是否执行后面的操作

### Action

当一个转换触发的时候执行的动作