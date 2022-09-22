# 在UE中的地编流程和注意事项

参考资料：[顽皮狗艺术家UE模块化搭建场景教程](https://www.bilibili.com/video/BV1H24y1Z7WM/?p=2&vd_source=81f507780c5f3724647df2b527363b64)

## Reference

> Big Picture --> Details --> Breakdowns --> Textures/Materials --> Bright Spot

在小项目中找美术参考和设计地图是可以同步进行的。

为项目的设计找参考是非常重要，也非常耗时的一步，但是做好这一步就已经能解决很多问题，譬如前期设计阶段没有定下来的地图问题、机关选择等，没有被找出的设计上的bug也可以在参考阶段被发现。

### Big Picture

整个项目的概念参考图，整体画风偏向哪种风格、光影处理呈现出怎样的氛围、游戏所在的位置、场景和年代等，都可以借助big picture实例化，在后续创作过程中被反复参考

### Details

包含场景中更近距离的细节问题，譬如地面的地砖是什么样子的，墙面是否会有花纹，街角的装饰物是什么样子的。

这一阶段不仅要选择“哪里会有需要参考的细节”，还需要指导“具体应该是什么样子”

### Breakdowns

模块化场景经常遇到的情况，就是怎么处理两个资产的衔接处，即可重复大量使用，同时可以完美融入环境，不会让玩家觉得突兀
视频中作者以自己展示的例子进行讲解，即两段地铁轨道的衔接处理，他采用了“停电的轨道内部”和“控制箱”，这样本身存在在地铁轨道内的场景，同时这种昏暗不起眼的衔接方式不会让玩家注意到

倒影中的礁石上的海草也是一种衔接吧（

### Texture/Materials

这不用多说吧

### Bright Spot

即使是完全写实的画风也需要凸显场景中最亮眼的部分是什么。这一部分用到的参考图最少，因为场景中的亮点非常个性化，例如草地中出现的超自然结构，怪奇物语的维克那也算（？

## Modeling

- 比例尺
-  Reference （scene and texture）：只是mesh阶段，不需要找精细的texture
- 测试：fbx倒入到UE进行测试

## Texture

对照reference，在纹理编辑器、渲染器里反复查看效果

## UE materials blending

不仅仅包含模型组合和材质添加，还会根据场景需要创建一些特殊纹理用于整体场景的painting

同时还要进行资产分类和组合，避免在引擎里查找资产过于麻烦

## Lighting and Post Process

UE打光殿堂级教程：[UE5打光  布光  环境氛围  讲解_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ef4y1o7Ru/?spm_id_from=333.337.top_right_bar_window_custom_collection.content.click)

UE官方光照文档：[光照快速入门指南](https://docs.unrealengine.com/4.27/zh-CN/BuildingWorlds/LightingAndShadows/QuickStart/)

UE中的光源分为Directional Light（就是平行的阳光啦），Point Light（向四面八方发光），Spot Light（只向某方向发射光的锥形光源），Rect Light和Sky Light等

### 后处理体积（PPV）

详细资料：[UE4-PostProcessVolume(后期处理盒子)_RollingTune的博客-CSDN博客_postprocessvolume](https://blog.csdn.net/qq_33500238/article/details/90344193)