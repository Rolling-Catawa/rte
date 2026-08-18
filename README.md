# Rollingcats Transit Expansion

面向 **Minecraft Transit Railway 4.0** 的轨道几何与建轨工具模组。
把轨道当成可计算的工程对象来处理 —— 曲线有半径、坡度有千分比、弯道有超高。

**Minecraft 1.20.1 · Fabric / Forge · 需要 MTR 4.0+**

---

## 下载

| 平台 | 地址 |
|---|---|
| Modrinth | *(待填)* |
| CurseForge | *(待填)* |
| MCMod 百科 | *(待填)* |

## 前置

| 模组 | 必需 | 版本 |
|---|---|---|
| Minecraft Transit Railway | 是 | 4.0.0+ |
| Architectury API | 是 | 9.2.14+ |
| Fabric API | Fabric 端必需 | 0.92.6+1.20.1 |
| MSD | 可选 | 装了才有接触网规划器 |

客户端必装。服务端可选 —— 装了才有完整路径、全量轨道与列车位置同步,不装自动退回原版行为。

---

## 主要功能

**轨道几何** —— 四种路径模式(原生 / 自动贝塞尔 / 多点曲线拟合 / 手动贝塞尔)、
可设半径的竖曲线、沿轨道分布的超高,车辆与镜头同步倾斜。

**建轨工具** —— 复合构建器画一次断面沿线扫出隧道桥梁,弯道自动转向、超高段自动滚转;
另有双线复制、沿线阵列、接触网规划器。

**旧版资源包兼容** —— MTR 3.x 包在 4.0 下会损失不少功能,本模组补这个断层:
双 JS 引擎(Rhino / GraalJS)、时间基准换算、贴图与门洞适配。
在 322 个社区资源包上实测,加载报错从 567 条降到 19 条。

**服务端数据同步** —— 完整行车路径、全量轨道分页传输、视口内列车位置。

**资源包工作台** —— 游戏内编辑车辆资源,导出为 JSON 或自包含资源包。

完整清单见 [FEATURES.md](FEATURES.md),操作说明见 [MANUAL.md](MANUAL.md)。

---

## 反馈问题

请开 [Issue](../../issues)。附上:

1. 模组版本与加载器(Fabric / Forge)
2. MTR 版本
3. `logs/latest.log`(必要时先 `/rte debug on` 复现一次)
4. 用到的资源包

---

## 已知问题

- 部分老包车辆的车头/车尾镜像异常,根因未定位
- 部分老包车辆的车厢位置错位,与上一条无关,同样未定位
- `legacyVehicleTakeover` 为实验性功能,几何已验证但视觉表现未逐项确认

详见 [MANUAL.md](MANUAL.md) 的「已知问题」与「尚未充分测试」两节。

---

## 关于本仓库

这里只放文档与问题反馈,**不含源码**。
本模组保留所有权利,详见 [LICENSE.txt](LICENSE.txt)。

第三方组件声明见 [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md)。

---

Copyright © 2025 Rolling-Catawa
