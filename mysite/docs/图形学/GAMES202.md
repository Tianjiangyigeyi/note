# GAMES202 高质量实时渲染

## Lecture1 Introduce

实时：30fps以上 

Interactive：10fps左右，一秒一帧两秒一帧is also acceptable

## Lecture2 Review

**渲染管线**：渲染就是由模型数据变成图像数据的过程，而渲染管线设计就是现在的gpu完成的工作，首先物体在在模型数据中会被表示为一系列的点和其连接关系，我们需要将其加载到gpu的显存中（VBO），然后进行主要的两个阶段，分别是几何阶段和光栅化阶段。

- 首先是几何阶段，我们需要对顶点进行处理

  - 首先使用vertex shader来处理这些顶点，将他们从世界坐标变化到NDC（model-view-projection），我们也可以在这里进行一些信息处理，如光照、阴影等；
  - 接下来是曲面细分着色器进行曲面细分（loop算法或者catmull-Clark算法）或者曲面简化（坍缩，二次误差）

  - 然后是几何着色器，由于并行化低，这里只适合进行顶点的增删等顶点着色器做不到的事
  - 接下来是几何阶段的最后一步，顶点后处理，将ndc中的标准设备坐标转换为屏幕坐标，把顶点信息存入显存中

- 现在开始光栅化阶段

  - 第一步图元组装，将顶点连接成面、线
  - 第二步遍历三角形光栅化，将三角形“打散”到像素（片元）里，这里可能会用到插值、抗锯齿
  - 第三步使用片元着色器，这里有很多功能可以实现，比如Phong/Blinn Phong等光照模型，可以自由地根据顶点数据获得想要的颜色
  - 第四步混合，进行各种测试，如裁剪测试（选择要渲染的部分）、透明测试（在OpenGL ES3.0中被删除，因为可以在片段着色器上进行）、模板测试（同样是选择要渲染的部分）、深度测试（在某些实现中会使用early-z的方法，即先进行深度测试选择需要的片元，但这又会导致透明物体出现问题，需要一些hack）、透明度混合、抖动

  通过了所有测试片元（像素）也不是直接画到屏幕上去的，其实这些像素可以输出到任何纹理图、缓冲或者屏幕上去，不过直接输出到屏幕不是一个好的做法，因为这样会导致画面撕裂，这就需要进行垂直同步，即输出到一个buffer中，等待buffer完全渲染完毕再交换当前显示的buffer和刚刚渲染好的buffer，这就是所谓双重缓冲。

##  Lecture3 Shadow(1)

###  Shadow Mapping

- 两趟（2-pass）算法
- 图像空间算法，不需要知道场景中的几何，但会导致自遮挡现象和走样（本质都是shadow map的分辨率太够高）

### Approximation in RTR

在实时渲染中，我们更关心“近似”相等

实时渲染中一个重要的近似：$\int_{\Omega} f(x) g(x) \mathrm{d} x \approx \frac{\int_{\Omega} f(x) \mathrm{d} x}{\int_{\Omega} \mathrm{d} x} \cdot \int_{\Omega} g(x) \mathrm{d} x$

所以可以把渲染方程里的visibility项单独提出来

### PCSS（Percentage closer soft shadows)

软阴影（阴影的界限很模糊）

**PCF**（Percentage Closer Filtering）：最先提出来是为了解决抗锯齿，后来发现在软阴影上也有应用，做法是计算着色点与shadow map中该点深度值的比较的时候，不仅采样该像素点的深度值，同时采样周边多个shadow map点深度值，逐一比较并求平均值，从而获得了一个从0到1的连续分布，能够表现不同明暗程度的阴影。

**PCSS**：不同遮挡物的深度决定不同的Filter size

![PCF&PCSS实现软阴影——GAMES202学习笔记](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202307311831831.jpeg)

## Lecture4 Shadow(2)

![image-20230801105918369](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308011059495.png)

### VSSM (Variance soft shadow mapping)

利用正态分布函数来求PCSS中“权”的近似值

 方差可以用公式$Var(X)=E(X^2)-E^2(X)$，其中$X^2$可以很方便地用shadow map的一个通道存储

  

## Lecture5 环境光照(1)（IBL）

**有向距离场SDF**，可以把定义中的“安全距离”换成“安全角度”即可用于shadow

![image-20230802180233268](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308021802382.png)

![image-20230802180309821](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308021803868.png)

该方法叫split sum approximation

 

##  Lecture6 环境光照(2)

### 球谐函数（Spherical Harmonics）

用一系列定义在空间中的二维基函数（只需要两维即可表示   三维空间中的一个方向）去拟合一个三维空间函数

**PRT**(Precomputed Radiance Transfer)：

- 我们把rendering equation分为两部分,lighting 和 light transport.

- 假设在渲染时场景中只有lighting项会发生变化(旋转,更换光照等),由于lighting是一个球面函数,因此可以用基函数来表示,在预计算阶段计算出lighting.

- 而light transport(visibility和brdf)是不变的,因此相当于对任一shading point来说,light transport项固定的,可以认为是shading point自己的性质,light transport总体来说还是一个球面函数,因此也可以写成基函数形式,是可以预计算出的.



## Lecture7 全局光照(1)

### RSM（Reflective Shadow Maps）

本质上还是应用一个shadow map，只不过不只存深度值，还要存世界坐标、法线、flux

### LPV（Light Propagation Volumes）

Steps:

1. 知道哪些点作为次级光源(virtual light point)（接受直接光照）
   - 使用RSM
2. 注入(inject)到场景中的3D格子中
   - 划分场景为3D纹理
   - 遍历每个格子，找到包含在其中的次级光源
   - 将次级光源的四面八方的radiance加起来，用sh表示（2阶即4个数可表示一个格子）
3. 传播（propagation)
   - 一个格子传播到相邻的六个格子中去（**每一个格子都认为是diffuse**）
   - 同样加起来用sh表示
   - 重复迭代直到稳定，大概三四次（所以这是一个四趟的算法）
4. 渲染
   - 对于每个着色点，找到他所位于的格子
   - 获取该格子所有入射的radiance
   - 着色

问题：当格子划分得比较大时，可能格子内部会有遮挡关系，以至于错误地向所有六个面propagation了，但是若划分得比较小，很大幅增加性能消耗，但是可以自适应格子的大小

### VXGI（Voxel Global Illumination）

也是两趟的算法，但是与RSM有一些区别：

1. 次级光源：RSM中是Texel，VXGI中是Voxel
2. 光线：RSM中是传播（从光源出发），VXGI中是追踪：从相机出发，打到每一个pixel上,根据pixel上代表的物体材质做出不同的操作,如果是glossy则打出一个锥形区域,diffuse则打出若干个锥形区域,打出的锥形区域与场景中一些已经存在的voxel相交,这些voxel对于Shading point的贡献可以算出来,也就是我们要对每一个shading point都做一个cone tracing,可想而知,这个速度比起LPV来说是很慢的,但是是可以优化的,暂且不提.

具体步骤：

1. **Light pass**：记录的是直接光源从哪些范围来（绿色部分），记录各个反射表面的法线（橙色部分），通过**输入方向**和**法线范围**两个信息然后通过表面的材质，来准确的算出出射的分布，这样就比LPV认为格子表面是diffuse再用SH来压缩的方法要准确，然后建立更高层级格子的这些特性。

   ![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308051411807.webp)

2. **Camera pass**：对于任何一个像素，知道了Camera Ray的方向。

   - 对于Glossy的表面，向反射方向追踪出一个锥形(cone)区域；基于追踪出的圆锥面的大小，对格子的层级进行查询，就是对于场景中的所有体素都要判断是不是与这个锥形相交，如果相交的话就要把对于这个点的间接光照的贡献算出来(我们存储了体素的光照输入方向和法线方向,因此可以算出其输出的radiance,将cone区域内所有体素的radiance都算出来从而在shading point得到间接光照)，也就是根据传播出的距离远近找对应层级的体素，然后找覆盖的范围。

     ![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308051413650.webp)

   - 对于diffuse的情况来说,通常考虑成若干圆锥，忽略圆锥Tracing时的重叠和空隙

     ![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308051413217.webp)

总结：

LPV是把所有的次级光源发出的Radiance传播到了场景中的所有位置，只需要做一次从而让场景每个Voxel都有自己的radiance，但是由于LPV使用的3D网格特性，并且采用了SH进行表示和压缩，因此结果并不准确，而且由于使用了SH因此只能考虑diffuse的,但是速度是很快的。

VXGI把场景的次级光源记录为一个层次结构，对于一个Shading Point，我们要去通过Corn Tracing找到哪些次级光源能够照亮这个点。

## Lecture8 全局光照(2) 

**屏幕空间（screen space）**

首先我们介绍一下我们讲了这么多的几种方法都是什么类型的:

- LPV和VXGI是属于在3D空间的GI
- RSM是属于在图像空间的GI
- SSAO和SSDO是属于屏幕空间的GI

什么是屏幕空间：

1. 使用的所有信息都来自“屏幕”，也就是做全局光照之前屏幕上能看到的信息，这些信息也就是做全局光照之前的直接光照信息；
2. 换一个角度说，是已经渲染过的场景的**后处理**
3. 为了与此区别我们把RSM与SM的方法称为图像空间，因为信息是来自从灯光看向的场景 所获得的信息。

### SSAO（Screen Space Ambient Occlusion 屏幕空间环境光遮蔽）

为什么要AO?

1. 易实现
2. 加强了物体的相对位置感

什么是SSAO?

1. 一个全局光照的近似
2. 在屏幕空间下

关键idea：

1. 我们不知道间接光照
2. 假设任何点来自任何方向的间接光照都是一样的一个**常数**，类似于phong模型中ambient
3. 但是不见得任何方向都能接受到上述的间接光照，所以需要考虑**visibility** 

原理：

![image-20230805144207185](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308051442244.png)

怎样在SS下？

![image-20230805145844905](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308051523711.png)

tips：如果有法线信息，则是HBAO(Horizon Based Ambient Occlusion)，可以防止出现两物体隔得很远也发生AO



## Lecture9 全局光照(3)

### SSDO（Screen Space Directional Occlusion)

什么是？

1. SSAO的提高
2. 考虑得更加精准

关键Idea：

1. 不用假设入射光是常量

2. 一些入射光的信息是已经知道的

3. 很像RSM，但不是from RSM，而是from 相机

4. 类似路径追踪，考虑与SSAO的比较，**完全相反**的思想：

   ![image-20230805152352124](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308051523193.png)

问题：

1. 只能小范围的GI
2. 需要visibility
3. P点对于半球上的点可见性是通过Camera对这些点的可见性来近似计算的，存在于屏幕空间中丢失信息的问题

### SSR（Screen Space Reflection）

说是Reflection，也可以理解是**Ray-tracing**

什么是？

1. 仍然是一种在RTR中实现GI的方式
2. 是在屏幕空间中做光线追踪
3. 不需要知道3D空间中的三角形、网格、加速结构等3D信息，只需要屏幕空间中已有的信息，也就是从camera看去场景的得到的这样一层“壳”。

两个基本任务：

1. 由于我们认为它是screen space raytracing,我们考虑的是**任何光线**(不单单是反射光)与场景中这层壳去做求交.
2. 找到交点后,算出对shading point的贡献值.



着色时：

这部分与路径追踪的方法完全相同，仅仅是把光线与场景求交变成了光线与“壳”求交，因此路径追踪的算法在这里是可以直接使用的。

对于任何一个shading point，看到的radicance就是对半球进行积分,如果是specular的物体,那么相当于光线打到物体的哪里,就用它所发出的radiance就可以.

![img](https://pic4.zhimg.com/80/v2-1b6ca88adf5481d5f8d35fc6ce3efdc3_720w.webp)

如果是glossy情况下,同样的用蒙特卡洛多采样几根光线,不管怎么所打到的物体反射过来的radiance,一定就是shading point点接收到的incident radiance.

**这里我们同样需要假设反射物/次级光源 是Diffuse的情况,地板之类的接收物可以是任何物体.**



## Lecture10 基于物理的材质(1)

实时渲染中的材质：

1. **基于物体表面上定义的材质**
   - 微表面
   - Disney Principled BRDF计算量比较轻量级，因此虽然产生初衷是为了能够用于离线渲染,但也可以运用在实时渲染中，这套材质的种类多效果也很不错,但也不是PBR，是基于artist的角度来考虑的。
2. **基于体积上定义的材质:**
   - 由于光线会进入到云,烟,雾,皮肤,头发等体积里,在RTR中基于体积上要比基于表面的困难许多,我们大部分考虑的还是光线在这些体积中作用一次(single)和多次(multiple)的分离考虑方法,这个在下节课学习.

### Microfacet BRDF

$$f(i,o)=\frac {F(i,h)G(i,o,h)D(h)}{4(n,i)(n,o)}$$

#### F : The Fresnel Term 菲涅尔项

表示观察角度与反射的关系(从一个角度看去会有多少的能量被反射)

有多少能量被反射取决于入射光的角度，当入射方向接近grazing angle掠射角度的时候，光线是被反射的最多的,也就是当你的入射方向与法线几乎垂直时候,反射的radiance是最多的.

由于光路的可逆性，我们可以认为眼睛看过去的方向是光线入射方向。

![img](https://pic1.zhimg.com/80/v2-407725dd0ae35fa6b343361d592aa124_720w.webp)

最右边的接近如grazing angle

下面是对于绝缘体反射率与角度的关系:

以河面为例,让你入射方向与河面法线平行,则会直接看到河底;如果入射方向与河面法线垂直,则能最大程度看到反射出的天空之类的.

![img](https://pic4.zhimg.com/80/v2-0367ee173e6f267aadf4887f2e5e3cb3_720w.webp)

绝缘体反射率与角度的关系



下面是对于导体反射率与角度的关系，与绝缘体不同，部分会出现反常现象

![img](https://pic3.zhimg.com/80/v2-14fb0db15371d3aabd8ff4239aed31d2_720w.webp)

导体反射率与角度的关系

**菲涅尔项告诉我们有百分之多少的能量会被反射出来.**

菲涅尔项的推导时要考虑光线的S极化和P极化效果，公式比较复杂,因为要考虑不同介质,如从空气到物体表面,各自的折射率和入射角折射角，最终推导出下图中的公式。

![img](https://pic2.zhimg.com/80/v2-06e4e030f68d6524e3ec029ea88e2e59_720w.webp)

但我们平常不用这个,而是用一个简单的近似：Schlick’s approximation

![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308061426926.webp)

我们之前讲过:

当θ->90度,cosθ=0,则R(θ)=1;

当θ->0度,cosθ=1,则R(θ)=R0;

其中R0(基础反射率)取决于物体,不同物体的R0各不相同.

#### D : Normal Distribution Function 表面分布函数

决定这一项的是不同微表面朝向的法线分布；

当朝向比较集中的时候会得到Glossy的结果,如果朝向特别集中指向时认为是specular的.

常用的有beckmann和GGX模型

##### beckmann：

其目的为了描述法线分布，因此肯定是一个关于法线方向h的函数，而h是半球上的任意一个方向，然后描述这一方向对应的值是多少，这就是NDF。

举个例子，给定向量h，如果我们的微平面中有35%与向量hℎ取向一致，则法线分布函数或者说NDF将会返回0.35。

##### GGX

Beckmann模型的NDF曲线与GGX模型的NDF曲线相比有一个明显的特点:

Long tail 长尾性质:

会很快衰减，但是衰减到一定程度的时候衰减速度会变慢,可以看到即使到了grazing angle(90度)时仍不为0。

![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202308061443694.webp)

这会带来两个好处:

1. Beckmann的高光会逐渐消失,而GGX的高光会减少而不会消失,这就意味着高光的周围我们看到一种光晕的现象.
2. GGX除了高光部分,其余部分会像Diffuse的感觉. 

![img](https://pic1.zhimg.com/80/v2-3a27926fd12ad0cf5a6ca2b2613386d4_720w.webp)



Beckmann模型与GGX模型的实际效果对比如图。

相同的粗糙程度下GGX的效果更加自然,因为long tail性质导致高光到非高光有一个柔和的过渡状态,而非Beckmann的高光到达grazing angle后戛然而止,我们希望的是像GGX一样的效果.

![img](https://pic3.zhimg.com/80/v2-66b15dfe06cc81c517382a5504b2445a_720w.webp)



#### G : Geometry term 自遮挡

可以解决边缘非常亮的问题

Kulla-Conty Approximation



## Lecture11 基于物理的材质(2)

放个链接，有点懒得做

[GAMES202 高质量实时渲染笔记Lecture11：Real-Time Physically-Based Materials (Surface models cont.) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/385438367)



## Lecture12 光线追踪(1)

RTRT: Real-Time-Ray-Tracing，最重要的工作是**降噪**，因为算力只能支持1spp（1 sample per pixel)

而降噪最重要的idea是**Temporal**（时间上）



## Lecture13 光线追踪(2)

滤波，双边滤波，联合双边滤波