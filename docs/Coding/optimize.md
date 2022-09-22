# 游戏性能测试及优化

## 游戏性能指标

资料来源：

- [游戏性能指标](https://www.cnblogs.com/kekec/p/7724865.html)
- [次世代美术资源性能优化](https://blog.51cto.com/dasein58/4583594)
- [静态批处理等](http://newhappy.com.cn/index.php/2020/05/14/batch/)
- [unity中的光照贴图](https://string.quest/read/1325229)
- [UE性能优化（程序）](https://zhuanlan.zhihu.com/p/55335907)
- [UE性能优化（美术）](https://zhuanlan.zhihu.com/p/55335653)

**CPU负载**

测试指标：综合CPU平均占用小于60%、单核CPU峰值占用小于90%

CPU影响因素：游戏逻辑、剔除算法、Cache Missing

优化途径：分帧、复用（内存池）、缓存、预加载、预计算、异步、多线程

**GPU负载**

测试指标：核心场景FPS均值、核心场景默认要求90%数值不低于60FPS

GPU影响因素：DrawCall数、图元数、shader复杂度、纹理采样、透明物件、OverDraw、RenderState频繁切换、带宽（Bandwidth）占用高

优化途径：合批、简化shader、遮挡剔除、裁剪、Billborad、Impostor

**FPS**

测试指标：核心场景FPS均值、核心场景默认要求90%数值不低于60FPS

**卡顿**

测试指标：关键路径和核心体验操作的卡顿率不高于1%、关键路径和核心体验操作没有严重卡顿问题

**内存**

测试指标：占用的物理内存峰值不超过2GB

***

## 检查手段

### Unity

Unity Frame Debugger-[Unity官方文档](https://docs.unity3d.com/Manual/FrameDebugger.html)

menu: Window > Analysis > Frame Debugger



概述：Frame Debugger 窗口会显示绘制调用信息，并允许控制正在构建的帧的“回放”

#### 关于Batches和SetPasses

参考：[什么是批处理](https://zhuanlan.zhihu.com/p/76562300)

Batches即绘制指令（Draw Calls），指在全局渲染状态下绘制相同shader、相同参数的对象
SetPasses描述的是材质设置，其中包含shader参数和管线设置

> Set Pass Call代表渲染状态切换，主要出现在材质不一致的时候，进行渲染状态切换。我们知道一个batch包括，提交vbo，提交ibo，提交shader，设置好硬件渲染状态，设置好光源属性等（注意提交纹理严格意义上并不包括在一个batch内，纹理可以被缓存并多帧复用）。如果一个batch和另一个batch使用的不是同种材质或者同一个材质的不同pass，那么就要触发一次set pass call来重新设定渲染状态。例如，Unity要渲染20个物体，这20个物体使用同种材质（但不一定mesh等价），假设两次dynamic batch各自合批了10个物体，则对于这次渲染，set pass call为1（只需要渲染一个材质），batch为2（向GPU提交了两次VBO，IBO等数据）。

### UE5

资料来源：[UE4如何评估画面性能](https://zhuanlan.zhihu.com/p/358728833)

1. 优先判断一个场景是GPU还是CPU瓶颈

~调出cmd，输入`stat unit`，Game代表CPU，GPU就是GPU

CPU负责场景中物体变换、位置、缩放、破碎等。GPU负责光照、材质等



2. 查看场景中模型面数

输入指令`stat rhi`，调出面板

