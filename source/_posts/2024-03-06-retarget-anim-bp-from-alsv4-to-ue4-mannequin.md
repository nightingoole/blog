---
title: ALS动画重定向到UE4Manny
date: 2024-03-06 16:30:19
description: 将ALSv4的动画蓝图重定向到UE4的小白人网格体上。
tags: [UnrealEngine]
---
# 准备

1. 准备一个干净的 ALS 项目模板
2. 在项目中添加 TPS 项目模板

在资产浏览器中点击 `Add`->`Feature Content`->`Third Person`->`Add to Project`

![](YukHbLgDRop5OTxB3mIcontrnEh.png)

得到如下两个资产包：

![](W5aMbcD6gonZw9xnshtc9x0WnFh.png)

# 创建 IKRig 资产

![](AuVwbZ77tobaEHxPvyIcRZ94n9N.png)

找到如图所示的模板包自带的 IKRig 资产，复制到 ALSv4 的项目位置下：

![](FF0MbnwfeoqXfJxzlafcyLw6nl8.png)

重命名资产为 `IK_AnimMan`，打开编辑面板：

![](AxuRbJHh4oOIEax4M2pc1aYxnlg.png)

根据骨骼实际情况调整右下方骨骼链的设置

随后新建 `IKRetargeter` 资产，取名为 `IKR_AnimMan_UE4Manny`:

![](WoE4b7gSNobYgKxbtOhc1Hxjnfd.png)

打开设置面板：

![](CK90b6OQQonkPExC6l9cPBkPnic.png)

在右侧细节面板分别选择源 IKRIG 资产，下方选择目标 IKRIG 资产。右下方骨骼链映射面板对 IKRIG 资产进行映射设置：

![](H4SzbqqkmoJckmxedqzcktYpn4e.png)

资产浏览器中对动画进行预览以查看最终重定向的效果：

![](PGhwb31X2o5XDdxUuoSc1qhMn7b.png)

# 动画蓝图设置

新建一个动画蓝图，取名为 `ABP_UE4Manny_Retargeter`，骨骼选择 `SK_UE4_Mannequin` 并打开设置面板：

![](Hs7Db0JBpofulcxxnY1ceBConma.png)

![](NcDlb1Znqo8lLix29R7czcASnqc.png)

新建节点 `Retarget Pose From Mesh`，并在细节面板中的 `IKRetargeter Asset` 选择创建好的 `IKR_AnimMan_UE4Manny` 资产

找到并打开 ALS 的角色蓝图 `ALS_AnimMan_CharacterBP`，将 BodyMesh 的骨骼网格图资产选择为 UE4 的 `SK_Mannequin`

![](IDOCbeB64oSp4Kx8cCZc9O7AnSd.png)

将根骨骼网格体可见性设置为不可见：

![](MSAfbRfMlo4flzx4cUyc4fYanuc.png)

最后运行场景，查看结果：

![](KYFzbQleGoZcjaxJtQeccwAfnKf.png)

# 参考

**UE5 - ALSv4 Retarget for Synty Polygon**
