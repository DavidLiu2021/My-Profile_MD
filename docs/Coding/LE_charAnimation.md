# Lone Echo中的角色动画机制

## 概述

Lone Echo为角色开发了手部的程序性动画系统，其效果表现为角色可以自由抓住环境中的“任何表面”，并采用IK算法模拟了双臂，以下的运动模拟采用角度约束逐级向下传递，与手臂的IK算法分开

参考资料：<https://zhuanlan.zhihu.com/p/27501307?utm_source=wechat_session&utm_medium=social&s_r=0>

## 针对LE团队在开发分享会上提及的问题

### 抓取物体

方法：采用基于物理的constraint替代手部射线检测（让身体与双手保持与真人1:1的相对位置来驱动身体）

原因：这样对于抓/推动态的环境物体效果不是很好. 原因是游戏逻辑的更新与物理引擎的更新会有次序上的问题。

![截屏2022-08-16 19.20.56.png](https://s2.loli.net/2022/09/22/WCqxkPj62bdcrgn.png)

关于Unity的物理引擎：<https://www.cnblogs.com/sylvan/p/13621322.html>

#### 基于物理的constraint

unity对于constraint的说明：[unity手册-创建游戏玩法-约束](https://docs.unity3d.com/cn/2020.3/Manual/Constraints.html)

![截屏2022-08-16 19.33.57.png](https://s2.loli.net/2022/09/22/gPSNABC2UizbQhq.png)

他们在头跟手之前也加了个Constraint, 这样手抓到移动的物体上就可以带动玩家进行漂浮移动了

![截屏2022-08-16 19.34.55.png](https://s2.loli.net/2022/09/22/81rK5twTELHQlf4.png)


### 为手部开发的程序性动画系统

概述：手抓物体的动作在多数VR游戏中都是使用美术预先制作的动画, 但LE中要求可以抓住世界中的任意表面。所以他们通过程序化的方式来计算手指抓住物体时的角度

针对单根手指来看这个问题, 其实就是求解三角形与圆盘的交点:

![截屏2022-08-16 19.44.51.png](https://s2.loli.net/2022/09/22/jXsUaRbl6VFAKvT.png)

1. A*搜索靠近手掌的最近三角形
2. 根据相交边找到相邻的三角形
3. 持续这个过程直到不在圆盘范围内
4. 计算与相交点接触的手指角度, 取最大的那个来旋转手指
5. 拇指使用另外的算法, 分享中没有提及

![截屏2022-08-16 19.46.00.png](https://s2.loli.net/2022/09/22/D8FyKcwH1WzCS4Q.png)

为了避免手与表面穿插, 需要在手掌与表面之间做一些距离和角度的约束.

![截屏2022-08-16 19.46.46.png](https://s2.loli.net/2022/09/22/dXVZQsOPForwpmn.png)

虽然这样做减少了一些走样, 但是射线拾取到错误的表面会导致手掌吸附到表面时的角度或位置偏转过大, 与真实手的位置不同步后一下就让人出戏了(影响VR体验). 所以需要选择一个同步代价最小的位置.

### 模拟双臂动画

概述：双臂的动画比较难模拟, 因为只有三个点的位置(朝向): 头, 左手, 右手。对于肩, 胸, 肘的信息是不知道的, 只能通过IK算法进行估算

*IK算法相关参考资料：*

  [逆向运动学（IK）详解（包含IK概念、IK算法、使用限制等，以及IK算法在UE4/5中的应用）](https://zhuanlan.zhihu.com/p/499405167)

  [角色动画研究 —— IK的三种结算方法](https://blog.csdn.net/noahzuo/article/details/53908141)

#### 手臂的IK估算过程

- Estimate chest facing - 估算胸部朝向

*Rotate model to face chest direction*

胸部的朝向通过混合三个方向的权重进行估算: 

头的观看方向, 头到左手的方向, 头到右手的方向 

运行时也会调整这些权重的分配, 因为手离胸近的时候它的权重就小了, 如果手在身后, 那就完全忽略掉它的影响

![截屏2022-08-16 20.15.13.png](https://s2.loli.net/2022/09/22/Z5v1xDEHScAT7Wr.png)

- Estimate arm extension (stretch) - 估算手臂的伸展

*Adjust arm joints to desired length*

手臂的长度是可以动态调整的, 因为虚拟手的位置必须与控制器的位置1:1进行同步

![截屏2022-08-16 20.16.45.png](https://s2.loli.net/2022/09/22/Ecp5KUzJ9sBj2Hd.png)

- Estimate clavicle extension - 估算锁骨的伸展

*Input extension angle and distance into an additive animation*

肩部位置的估算涉及到锁骨的伸展和朝向. 

朝向指向手的方向, 伸展通过一条手肩距到伸展量的曲线映射进行估算

![截屏2022-08-16 20.21.40.png](https://s2.loli.net/2022/09/22/4vBEoWmHiUI98T2.png)

- Estimate elbow pole vector - 估算肘极向量

*Blend between a base rest pose and wrist direction*

在肩与手的位置确定的情况下, 只需要估算肘部的转动角度, 肘部的弯曲角度只与肩到手的距离有关系, 是确定可以计算出来的

![截屏2022-08-16 20.22.17.png](https://s2.loli.net/2022/09/22/3fTQkqgGlER4BYe.png)

- Two bone IK solve for arms

<https://mrl.cs.nyu.edu/~perlin/gdc/ik/ik.java.html>



### 模拟脊柱和双腿

概述：对于脊柱和双腿的运动模拟, 主要是通过角度约束从脖子一级级往下传递, 与双臂的IK是分开计算的. 由于《Lone Echo》是一个太空行走游戏, 所以不需要处理双腿与地面的走路/下蹲等, 只需要根据位置和速度进行模拟跟随躯干即可

对于待机/推开/观看等, 是通过叠加动画师制作的动画来实现的