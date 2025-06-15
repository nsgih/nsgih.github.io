---
layout: post
title: 五三
date: 2025-06-02 18:59 +0800
tag: complex
---

关于定理。

## *梅氏定理@wikipedia

梅涅劳斯定理（Menelaus' theorem）最早出现在古希腊数学家梅涅劳斯的著作《球面三角学》，`定理的平面版本`被用作证明`该定理的球形版本`的引理。对于初中数学来说，属于平行线段成比例问题的高层次抽象模型，模型范围覆盖`三角形被直线所截`类问题。与`塞瓦定理的等式`在条件上有所不同，二者互为对偶定理。

### 正定理

它指出：如果一直线与△ABC的边BC、CA、AB或其延长线分别交于L、M、N，则有：  

![alt text](/assets/2025-05/image-48.png)_正定理_ ∎

![alt text](/assets/2025-05/image-46.png)_case-1:直线LNM穿过三角形ABC_
![alt text](/assets/2025-05/image-47.png)_case-2:直线LNM在三角形ABC外面(M与N位置可能有错)_

### 逆定理(也成立，可用于证明三点共线)

若有三点L、M、N分别在△ABC的边BC、CA、AB或其延长线上（有一点或三点在延长线上），且满足：

![alt text](/assets/2025-05/image-49.png)_逆定理_

则L、M、N三点共线。 ∎

### 证明

#### @平行线

![alt text](/assets/2025-05/f0170309482ba0a841a0cf336f26de0.jpg)_基础case证明_
![alt text](/assets/2025-05/46ca93401c8c2b229db7718071fb068.jpg)_推广case证明_

#### @面积法SHM

![alt text](/assets/2025-05/image-46.png)_case-1:直线LNM穿过三角形ABC_

如情况一，连接AL、CN有：

![alt text](/assets/2025-05/image-50.png)_SHM_ ∎

#### @正弦定理

![alt text](/assets/2025-05/image-46.png)_case-1:直线LNM穿过三角形ABC_

如情况一，设∠ANM=α，∠AMN=β，∠MLC=γ，则在△AMN中由正弦定理，有：

![alt text](/assets/2025-05/image-51.png)_正弦定理_ ∎

## *塞瓦定理@wikipedia

塞瓦定理（Ceva's theorem）最先由意大利数学家乔瓦尼·塞瓦（Giovanni Ceva，1647年12月7日—1734年6月15日）证明，另外，他重新发现了梅氏定理。

塞瓦线、塞瓦线段，指各顶点与其对边或对边延长线上的一点连接而成的线段。

### 正定理

![alt text](/assets/2025-05/image-54.png)_case1-三条线段的交点O 位于三角形ABC的内部_
![alt text](/assets/2025-05/image-55.png)_case2-三条线段的交点O 位于三角形ABC的外部_

塞瓦定理指出：如果△ABC的塞瓦先线段AD、BE、CF交于一点O，则：

![alt text](/assets/2025-05/image-52.png)_塞瓦定理_ ∎

### 逆定理（同样成立，可用于证明三线共点）

它的逆定理同样成立：若D、E、F分别在△ABC或其延长线上（都在边上或有两点在延长线上），且满足：

![alt text](/assets/2025-05/image-53.png)_塞瓦逆定理_ 

则直线AD、BE、CF共点或彼此平行（于无限远处共点）。当AD、BE、CF中任两条直线交于一点，则三直线共点；当AD、BE、CF中任两条直线平行，则三直线彼此平行。∎

### 证明

![alt text](/assets/2025-05/image-54.png)_case1-三条线段的交点O 位于三角形ABC的内部_

![alt text](/assets/2025-05/image-56.png)_证明_ ∎

### 对偶定理的讨论

塞瓦定理讲的是三条线交于一点，梅涅劳斯定理讲的是三点共线。而这两种情况在几何学里是经典的对偶关系：
- 一点可以看作是多条线的“汇聚”（共点）。
- 一条线可以看作是多点的“轨迹”（共线）。

它们“交换了”点和线的角色。
