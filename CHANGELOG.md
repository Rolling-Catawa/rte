# Changelog

Rollingcats Transit Expansion 的版本更新记录。
Release notes for Rollingcats Transit Expansion.

---

## 1.0.0-beta.2

### English

227 commits since beta.1. **All beta.1 users should update** — three separate
startup crashes are fixed here, including one that made the Forge build unusable
alongside another popular addon.

#### Startup crashes

- **Fixed the Forge build failing to launch when FangSu MTR Addon is installed.**
  Both mods bundled GraalJS under its original package names, and Forge's module
  system rejects two modules exporting the same package:
  `ResolutionException: Modules fangsu and rte export package
  com.oracle.truffle.api.exception`. The game never reached the mod loading stage.
  Every bundled dependency is now relocated under `cn.jstxjf_.rte.libs`, so RTE no
  longer collides with anything. Fabric was unaffected because it does not use JPMS.
- **Fixed a crash on Java 22 and newer.** The bundled GraalJS depends on an internal
  method removed in JDK 22. RTE now detects this and uses Rhino directly instead of
  attempting Graal and failing once per script. Rhino works on 17, 21 and 24.
- **Fixed the dedicated server crashing during item registration.**
  `NoClassDefFoundError: net/minecraft/class_437` — a client-only `Screen` type
  appeared in a method signature check, which the bytecode verifier resolves at class
  load time even though the code never runs server-side. Screen construction now
  happens inside a client-only class.
- **Fixed a hard crash that could terminate the game outright.** A race between the
  script thread uploading an LCD texture and the main thread reclaiming it wrote to
  freed native memory (`EXCEPTION_ACCESS_VIOLATION`). Most likely on trains with many
  displays.

#### New — compatibility with other MTR addons

RTE, JCM, FangSu and S1 all patch the same MTR code. When two mods hook the same
place and cancel the original, only the first one runs — **the other silently stops
working with no error**. That is the "installing A broke B" class of bug.

- **Added an arbitration prompt.** On first launch with any of these installed, RTE
  asks who should own each area: track angle maths, node right-click, track preview
  angles, one-way arrows, mesh de-duplication, vehicle scripts, eye-candy scripts.
  Where two addons contest the same area you can decide per addon.
- **Defaults are not "RTE always wins."** Mesh de-duplication defaults to JCM, whose
  implementation is an O(n) hash pass against RTE's "skip past a face limit".
- **Added eye-candy script de-duplication.** Previously only vehicle scripts were
  de-duplicated against JCM; scripted decorations rendered twice with no way to stop it.
- **Added `ModelManager.upload` as an alias.** Scripts loaded from the `mtrsteamloco`
  namespace are supplied by JCM but execute in RTE's engine, so the two APIs must
  agree on names.
- **Lowered mixin intrusiveness.** Several `@Overwrite` hooks became cancellable
  injections, so other mods hooking the same methods still run.

#### New — script `use` callback

Scripts can now declare clickable regions and receive right-clicks, on vehicles and
on decorations. Buttons, driving desks and manual door controls become scriptable.

```js
function create(ctx, state, train) {
    ctx.addUseBox("door_left", 0, -1.4, 1.0, -1.2, -1.2, 1.4, -0.8);
}
function use(ctx, state, train, event) {
    if (event.box === "door_left") { state.doorRequested = true; }
}
```

The event carries the hit point in local coordinates, distance, look direction, held
item and sneak state. **The coordinates are the same ones you draw the model in** —
no conversion needed. Scripts that register no regions are not affected at all.

#### New — Minecraft 1.20.4

The common module now compiles for both 1.20.1 and 1.20.4 through a source
preprocessor, and `collectJars` produces all four artifacts with SHA-256 checksums.
1.20.4 builds are confirmed to compile and load; in-game features are not yet
individually verified.

#### Resource Workbench — expanded

The in-game editor for vehicle resources shipped in beta.1; this release adds:

- Recursive model and part editor with enum dropdowns
- bbmodel import and local texture hot-swap
- Textured model viewport with element rotations applied
- Persistent copies via autoload, and export to a distributable resource pack zip

#### Catenary planner — expanded

- Plan the whole run inside the GUI with a top-view preview
- MSD model catenary blocks with facing and mirror variants
- Poles align perpendicular to the rail tangent; one-click yaw presets when a pack
  needs a different reference orientation
- Pole block offset is separate from wire position
- NaN nodes and runaway spans are rejected rather than producing broken geometry

#### Compound creator — expanded

- **Segmented slices** — cycle several cross-sections along the track, each with its
  own length. Click a segment to edit it on the canvas.
- **Piers** — drop a column straight down at a fixed spacing, stopping at the first
  non-air block.
- **Capture from world** — stand where the track will run, face along it, and pull the
  surrounding cross-section straight into the template.
- **Templates now store blocks by name** (`namespace:id[properties]`) instead of a
  numeric palette id, so a saved template survives changing your mod set. Old
  templates still load.

#### Track editing

- All three path editors share one canvas and toolbar layout, drawn through ImGui
- Rail path editor gained a geometry rating and view controls
- User-editable rail chain selection, with endpoint handles draggable in the world
- Chain smoothing uses adaptive control arms and angle-bisector tangents, and rolls
  itself back when it would introduce a kink
- Manual bezier endpoints snap to neighbour tangents sampled from geometry
- `R` bound to look-ray rail editing

#### Roll / superelevation

Substantial rework so that the car body, bogies, rails and riding camera all agree:

- Camera roll resolves at frame start and holds through starved frames
- Body and bogies bank about the rail plane using the same per-frame sample
- Rails draw with the same frame's roll as the world
- Roll only applies to the car the player is actually riding
- Roll sampling now matches ANTE's contract, including the pivot wrap
- One-click seam alignment for roll angles

#### Railway dashboard

- Routes coloured by pathfinding each line, with animated drawing
- Solid lines, labels and resilient segments on the map
- Rail archive so the map survives depot data dropping out

#### MTR 3.x resource pack compatibility

- `default.png` matched by filename, not by exact path
- `pack_format` check relaxed for packs built for older Minecraft versions
- Illegal resource paths get a legal alias generated
- Door direction, door side and `DOORWAY` part synthesis corrected
- Gangways use the pack's own textures, and legacy car spacing was restored
- Missing dependency packs are summarised instead of throwing per entry
- Missing textures render untextured rather than as the missing-texture checkerboard
- Oversized textures are downsampled on read
- Script paths tolerate uppercase and invalid characters
- Collections get array semantics under Rhino
- **Experimental**: `legacyVehicleTakeover` parses legacy obj vehicle definitions
  directly, bypassing MTR's conversion chain

#### Performance

- **Vehicle scripts run in parallel across vehicle types.** Previously all vehicles
  and decorations shared one script thread, so LCD refresh rate fell as more trains
  came into view even at a healthy frame rate.
- **Per-vehicle engine selection.** `scriptEngineOverrides` accepts wildcards, so heavy
  scripts can use GraalJS while everything else stays on Rhino for compatibility —
  no longer an all-or-nothing choice.
- Repeatedly failing script functions are backed off, fixing an exception storm that
  could exhaust memory
- Texture downsampling rewritten to use direct memory access
- bbmodel preloading capped by count and heap headroom, per reload rather than on a timer
- Preloaded vehicle models load on demand instead of at registration

#### Other fixes

- **Decoration collision boxes were rotated 180° away from their model** on every
  facing. This went unnoticed because all three built-in decorations are
  symmetric; only asymmetric custom shapes showed it.
- **Missing optional resource packs no longer open an error screen.** "Pack X is not
  installed, 27 decorations skipped" was reported as an exception with a full stack
  trace, indistinguishable from a real crash, on every launch.
- Fixed asymmetric coupling gaps — only the front padding had been overridden
- Fixed LCD station timing reading from two different path lists once server-side
  path sync engaged
- Fixed dwell being reported as a depot return
- Fixed platform distance and the built-in LCD layout
- Fixed car length rounding that misaligned couplers
- Removed D51 and DK3 assets originating from NTE

#### Interface

- All UI strings are now available in both English and Simplified Chinese
  (515 entries each, no gaps); Traditional Chinese and five other languages fall back
  to English where untranslated
- Log messages are English throughout, since logs get attached to issue reports
- Brush editing and MTR screen replacement are now separate options —
  `rteBrushEditing` (on) and `overrideMtrScreens` (off)

#### Still unresolved

- Front/rear mirroring is wrong on some legacy pack vehicles. RTE's own vehicle mixins
  were ruled out by disabling all fifteen of them, and MTR 4's `mirror`/`reversed`
  conversion was verified case by case against decompiled source. Root cause unknown.
- Car positions are offset on some legacy pack vehicles, unrelated to the above and
  likewise unlocated.
- The route path creator can crash when right-clicking air.
- Enabling bbmodel preloading hides catenary masts and other decoration models —
  MTR's `CachedResource` uses a static gate allowing one resource load per tick, and
  preloading hundreds of vehicles starves everything else. Workaround: leave it off.

---

### 中文

相对 beta.1（2026-07-17）的 227 个提交。**beta.1 用户请尽快更新** ——
本版修掉了三个各自独立的启动崩溃，其中一个使 Forge 版在与另一个常用附属共存时完全不可用。

#### 启动崩溃

- **修复 Forge 版在装有方速 MTR 扩展时无法启动。** 两个模组都以原包名内嵌了 GraalJS，
  而 Forge 的模块系统拒绝两个模块导出同一个包：
  `ResolutionException: Modules fangsu and rte export package com.oracle.truffle.api.exception`。
  游戏根本走不到模组加载阶段。现已把全部内嵌依赖重定位到 `cn.jstxjf_.rte.libs` 之下。
  Fabric 不走 JPMS，故不受影响。
- **修复 Java 22 及以上版本的崩溃。** 内嵌的 GraalJS 依赖一个在 JDK 22 中被移除的内部方法。
  现会直接改用 Rhino，不再每个脚本都先失败一次。Rhino 在 17 / 21 / 24 上均可用。
- **修复专用服务端在注册物品时崩溃。** `NoClassDefFoundError: net/minecraft/class_437` ——
  客户端专属的 `Screen` 类型出现在方法签名的可赋性检查中，字节码校验器会在类加载期解析它，
  哪怕那段代码在服务端永远不会执行。现已把界面构造移入客户端专属类。
- **修复一个会导致游戏直接退出的崩溃。** 脚本线程上传 LCD 贴图与主线程回收贴图之间存在竞态，
  会写入已释放的原生内存。显示屏较多的列车更容易触发。

#### 新增 —— 与其他 MTR 附属的兼容仲裁

RTE、JCM、方速、S1 都会改动 MTR 的同一批代码。两个模组在同一处注入并取消原版时，
只有先跑的一方生效，**另一方静默失效且不报错** —— 这就是「装了 A 之后 B 就没了」的成因。

- **新增仲裁弹窗。** 首次与这些附属共存时询问各领域由谁接管：轨道角度运算、节点右键、
  轨道预览角度、单向箭头、网格去重、车辆脚本、眼糖脚本。同一领域有多个附属在场时可分别设置。
- **默认值并非「RTE 全都要」。** 网格去重默认让给 JCM —— 其实现是 O(n) 哈希去重，
  而 RTE 只是超过面数上限就跳过，前者严格更优。
- **新增眼糖脚本去重。** 此前只有车辆脚本会与 JCM 去重，带脚本的装饰会被渲染两遍且无从关闭。
- **补 `ModelManager.upload` 别名。** 以 `mtrsteamloco` 命名空间分发的脚本由 JCM 提供，
  却在 RTE 的引擎中执行，两边的 API 命名必须对得上。
- **降低 mixin 侵入性。** 若干 `@Overwrite` 改为可取消的注入，其他模组在同一方法上的注入仍能执行。

#### 新增 —— 脚本 use 回调

脚本可以为车厢和装饰物件划出可点击区域并接收右键。按钮、驾驶台、手动开关门都能脚本化。

事件带命中点的局部坐标、距离、视线方向、手持物品与潜行状态。
**这套坐标就是你画模型用的坐标**，不需要换算。没登记区域的脚本完全不受影响。

#### 新增 —— Minecraft 1.20.4

common 模块经源码预处理器可同时编译 1.20.1 与 1.20.4，`collectJars` 一次产出四个构件并附
SHA-256。1.20.4 已确认可构建与加载，游戏内功能尚未逐项验证。

#### 资源包工作台 —— 扩充

资源包工作台在 beta.1 已经提供，本版新增：递归的模型与部件编辑器（枚举下拉）、
bbmodel 导入与本地贴图热替换、带贴图的模型预览视口（应用元素旋转）、
经自动加载持久化的副本，以及导出为可分发的资源包 zip。

#### 接触网规划器 —— 扩充

全程在界面内完成，带俯视预览；支持 MSD 模型接触网方块的朝向与镜像变体；
支柱垂直于轨道切线对齐，并提供一键朝向预设；支柱方块偏移与导线位置分离；
拒绝 NaN 节点与异常跨距。

#### 复合构建器 —— 扩充

**分段切片**（沿轨道循环铺多种横截面，各段可设不同长度）、
**桥墩点式分布**（每隔固定距离向下打柱，撞到非空气即停）、
**从世界抓取**（站到轨道将经过的位置一键吸取横截面）。
切片模板改按 `namespace:id[properties]` 存储，换整合包后仍能正确还原，旧模板照常可读。

#### 轨道编辑

三个路径编辑器统一到同一套画布与工具栏；轨道路径编辑器新增几何评级与视图控制；
轨道链可手动选择，端点手柄可在世界内拖动；链条平滑改用自适应控制臂与角平分线切线，
会在引入折角时自动回滚；手动贝塞尔端点吸附到由几何采样得出的邻接切线；`R` 键绑定视线射线编辑。

#### 超高与滚转

车身、转向架、轨道与乘车视角现已统一：滚转在帧首解析并在掉帧时保持；
车身与转向架绕轨道平面倾斜，用的是同一份逐帧采样；轨道以与世界同一帧的滚转绘制；
只对玩家实际乘坐的那节车应用；采样语义与 ANTE 对齐（含枢轴换算）；滚转角提供一键接缝对齐。

#### 铁路仪表板

按线路寻路着色并带动画绘制；地图改为实线并加标签，分段更健壮；
新增轨道归档，车厂数据掉线时地图仍可用。

#### MTR 3.x 资源包兼容

`default.png` 按文件名匹配；放宽 `pack_format` 校验；为不合法的资源路径生成合法别名；
修正车门朝向、开门侧与 `DOORWAY` 部件合成；贯通道改用资源包自带贴图并补回老包车厢间距；
缺失依赖包改为汇总而非逐条抛异常；缺失贴图改为不贴图而非显示缺失纹理；
超大贴图读取时降采样；脚本路径容忍大写与非法字符；Rhino 下为集合补齐数组语义。
**实验性**：`legacyVehicleTakeover` 直接解析老包 obj 车辆定义，绕开 MTR 的转换链。

#### 性能

**车辆脚本按车型并行执行** —— 此前所有车辆与装饰共用一条脚本线程，视野内列车越多显示屏刷新越慢，
即使帧率正常也如此。**引擎可按车辆指定** —— `scriptEngineOverrides` 支持通配，
重脚本走 GraalJS、其余留 Rhino 保兼容，不再是全局二选一。
反复失败的脚本函数会退避，修复了每帧抛异常导致内存耗尽的问题；
贴图降采样改用直接内存访问；bbmodel 预加载按数量与堆余量设限，且改为按重载而非定时；
预加载的车辆模型改为按需加载。

#### 其他修复

- **装饰物件的碰撞箱与模型恒差 180°**，四个朝向全部错位。此前未被发现，
  是因为内置的三个装饰形状全部中心对称，只有非对称的自定义形状才会暴露。
- **缺少可选资源包不再弹出错误界面。**「未安装某包，27 个装饰跳过」此前被报告为带完整堆栈的
  异常，与真实崩溃无从区分，且每次启动必弹。
- 修复车钩间距前后不对称 —— 此前只重写了前端间距
- 修复服务端路径同步生效后 LCD 站点判断读取了两份不同的路径列表
- 修复停站被误判为回厂、站台距离与内置 LCD 版面、车厢长度取整导致的连接件错位
- 移除源自 NTE 的 D51 与 DK3 素材

#### 界面

全部界面文案现已中英齐备（各 515 条，无缺失），繁体中文与其余五种语言在未翻译处回落英文；
日志文案统一为英文，因为日志是要贴进 issue 的；
刷子编辑与 MTR 界面替换拆分为两个独立开关 —— `rteBrushEditing`（开）与 `overrideMtrScreens`（关）。

#### 仍未解决

- 部分老资源包车辆的车头/车尾镜像异常。已停用全部 15 个车辆 mixin 排除 RTE 自身，
  并对照反编译源码逐 case 验证了 MTR 4 的 `mirror`/`reversed` 转换本身正确。根因未定位。
- 部分老资源包车辆的车厢位置错位，与上一条无关，同样未定位。
- 线路路径创建器对空气右键可能崩溃。
- 开启 bbmodel 预加载会使接触网支柱等装饰模型不渲染 —— MTR 的 `CachedResource` 用静态闸门限制
  每 tick 只加载一个资源，预加载数百辆车会饿死其它模型。规避办法：保持关闭。
