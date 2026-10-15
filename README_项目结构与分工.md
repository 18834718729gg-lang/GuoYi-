# 《中国五千年》Unity 工程结构与分工规则

> 定位：本文档是 **Unity 工程内文件夹结构与脚本 ownership 的唯一权威**——架构拆解决定「实现什么」，本文决定「放哪里、谁能改」。
> 配套：桌面 `比赛策划案/` 下的架构拆解四件套（总览 / 逻辑层主程 / 表现层主程 / 编辑器）+《程序分工表》《死锁标记》《团队协作使用指南》。
> 生效：2026-10-15 ｜ Unity 2022.3.62f3c1 ｜ 工程根 = `D:/unity项目/Chinese`

---

## 1. 目录结构（已建好，直接往里放文件）

```
Assets/
├── Chinese5000/
│   ├── Scripts/
│   │   ├── Bootstrap/    组合根（GameBootstrap.cs）
│   │   ├── Core/         SO 类型 + 纯 C# 模型（NodeSO/DynastySO/CurrencySO/ExchangeRateSO/NodeEntry/Formula/SaveData）
│   │   ├── Systems/      规则引擎（EconomyEngine/CurrencyWallet/ProgressionManager/NodeGraphRuntime/GameStateManager/NodeRevealService）
│   │   │   └── SideQuest/  支线五件套（M7）
│   │   ├── Data/         SO 事件通道与变量类型（GameEvent/DoubleVariable）
│   │   ├── UI/           表现层全部视图组件（MonoBehaviour）
│   │   ├── Save/         存档接口与实现（ISaveStore/JsonSaveStore/SaveMigrator）
│   │   └── Editor/       编辑器工具（仅 Editor 程序集，XlsxImportWindow 等）
│   ├── Data/
│   │   ├── Databases/    总库资产（NodeDatabase/DynastyDatabase/CurrencyDatabase/ExchangeRateTable）
│   │   ├── Events/       事件通道资产实例
│   │   └── Packs/        支线内容包资产
│   └── Prefabs/          自包含视图预制体（NodeView/PedestalView/HUD…）
├── Scenes/
│   ├── Game.unity        持久 App 场景（Bootstrap + HUD 常驻，additive 宿主）
│   ├── Museum3D.unity    3D 收藏馆（additive 子场景）
│   └── Tree2D.unity      2D 技术树（additive 子场景）
└── （SampleScene.unity 为模板残留，M0 由逻辑层主程确认后删除）
```

> 场景说明：`Game.unity` 即架构文档中的「App 持久场景」——Bootstrap 与 HUD 常驻，Museum3D / Tree2D 以 additive 方式加载卸载（禁 DontDestroyOnLoad）。

---

## 2. Ownership 矩阵（核心规则：谁可以改 / 新增哪个文件夹）

| 文件夹 | 逻辑层 | 表现层 | 编辑器 | 备注 |
|---|---|---|---|---|
| `Scripts/Bootstrap/` | ✅ 写（主程亲自） | ❌ | ❌ | GameBootstrap 是唯一装配点 |
| `Scripts/Core/` | ✅ 写 | ❌ 只读 | ❌ 只读 | SO 字段即契约，改动须双文档同步 |
| `Scripts/Systems/`（含 SideQuest/） | ✅ 写 | ❌ 只读 | ❌ 只读 | 规则引擎 |
| `Scripts/Data/` | ✅ 写 | ❌ 只读 | ❌ 只读 | 事件通道类型 |
| `Scripts/Save/` | ✅ 写 | ❌ 只读 | ❌ 只读 | |
| `Scripts/UI/` | ❌ | ✅ 写 | ❌ | 全部 MonoBehaviour 视图 |
| `Scripts/Editor/` | ❌ | ❌ | ✅ 写 | 仅 Editor 程序集 |
| `Data/`（SO 资产实例） | ❌ 只读 | ❌ 只读 | ✅ 写（导入器生成） | 数值策划可在 Inspector 填值（资产非脚本，不走死锁，群里同步即可） |
| `Prefabs/` | ❌ | ✅ 写 | ❌ | 视图预制体 |
| `Scenes/Game.unity` | ✅ 建骨架 | ⚠️ 联合（挂 HUD） | ❌ | 场景文件按死锁协议占用，动前查表 |
| `Scenes/Museum3D.unity` `Tree2D.unity` | ❌ | ✅ 写 | ❌ | |
| `Packages/` `ProjectSettings/` | ❌ 禁改 | ❌ 禁改 | ❌ 禁改 | 仅组长（装包/改设置时） |
| `Library/` `Temp/` `obj/` `.sln` `*.csproj` | ❌ 禁改 | ❌ 禁改 | ❌ 禁改 | Unity 自动生成，手改必坏 |

**执行方式**（文件夹没有技术锁，靠三件事）：
1. 脚本级互斥走《死锁标记.xlsx》四步协议（动前占位、动完复位）；
2. 文件夹级归属走本矩阵——**闯进别人 ✅ 的文件夹 = 主程评审打回**；
3. 跨组需求（如表现层要新查询 API）走主程一对一，改完同步架构文档，不私改。

---

## 3. 脚本 → 文件夹 → 归属组对照

### 3.1 已录入《程序分工表》/《死锁标记》的 32 项（锁定的，按表施工）

| 脚本 | 文件夹 | 组 | 里程碑 |
|---|---|---|---|
| GameBootstrap.cs | Scripts/Bootstrap/ | 逻辑层 | M1 |
| NodeEntry.cs / Formula.cs / SaveData.cs | Scripts/Core/ | 逻辑层 | M2 / M2 / M5 |
| NodeSO.cs / DynastySO.cs / CurrencySO.cs / ExchangeRateSO.cs | Scripts/Core/ | 逻辑层 | M2–M4 |
| NodeGraphRuntime.cs / EconomyEngine.cs / CurrencyWallet.cs / ProgressionManager.cs | Scripts/Systems/ | 逻辑层 | M2–M4 |
| ISideQuestProvider / SideQuestRegistry / SideQuestPack / SideQuestManager / SideQuestPortalNode | Scripts/Systems/SideQuest/ | 逻辑层 | M7 |
| GameEvent.cs / DoubleVariable.cs | Scripts/Data/ | 逻辑层 | M6 |
| ISaveStore.cs / JsonSaveStore.cs / SaveMigrator.cs | Scripts/Save/ | 逻辑层 | M5 |
| NodeView / TimelineView / DynastyPanelView / EventPanelView / StatsView / AchievementView / SourcePopupView / SettingsView | Scripts/UI/ | 表现层 | M2–M6 |
| XlsxImportWindow.cs | Scripts/Editor/ | 编辑器 | M2 |
| Game.unity | Scenes/ | 逻辑层 | M0 |

### 3.2 架构要求但两表未录的脚本（**待补录**，主程按《协作指南》FAQ Q1 加行）

| 脚本 | 文件夹 | 组 | 里程碑 | 来源 |
|---|---|---|---|---|
| GameStateManager.cs | Scripts/Systems/ | 逻辑层 | M1 | 架构 §4.1 状态门面 |
| NodeRevealService.cs | Scripts/Systems/ | 逻辑层 | M2 | 架构 §4.6 锁定态揭示（§6.5.3.1） |
| SceneDirector.cs | Scripts/UI/ | 表现层 | M1 | 架构 §1 场景切换 |
| TapVsDragInput.cs | Scripts/UI/ | 表现层 | M1 | 架构 §5 输入分流 |
| CameraOrbit.cs | Scripts/UI/ | 表现层 | M3 | 架构 §2.2 相机 |
| PedestalView.cs | Scripts/UI/ | 表现层 | M3 | 架构 §2.1 底座+雕塑 |
| HudCurrencyView / HudPlaytimeView / HudSwitchView | Scripts/UI/ | 表现层 | M1 | 架构 §4 HUD 三件套 |
| SidePortalView.cs | Scripts/UI/ | 表现层 | M6 | 架构 §6 支线入口 |
| Museum3D.unity / Tree2D.unity | Scenes/ | 表现层 | M2/M3 | 架构 §1 双场景 |
| NodeSODrawer / ExchangeRateTableEditor / SideQuestPackValidator / ArchitectureSelfCheck | Scripts/Editor/ | 编辑器 | M2 后 | 编辑器文档 §2 P1/P2 |

---

## 4. 命名对齐表（消除「表 vs 架构 vs 策划案」漂移）

| 《分工表》/《死锁标记》 | 架构拆解文档 | 策划案 | 裁定 |
|---|---|---|---|
| EconomyEngine.cs | Calculator | Calculator（§5.3/§6.5.5） | **文件与类名用 EconomyEngine**（表已锁定 32 行，改表成本高）；`AddCurrency` 等 API 语义不变，架构文档 §1 术语表已加别名 |
| OnNodeUnlocked（死锁标记 #18 简述） | OnNodeBought | OnNodeBought（§5.3） | **以策划案 OnNodeBought 为准**；M1 主程定稿后把表里简述改齐 |
| Game.unity（唯一主场景） | App 持久场景 | — | **同一物**：Game.unity = App 持久场景；架构文档已改名对齐 |
| NodeEntry.cs（纯 C# 节点模型） | NodeEntry / NodeState | NodeEntry（§5.1） | NodeEntry.cs = 运行时模型（Core/）；NodeSO.cs = 配置资产，两者分工见表 §3.1 |

---

## 5. 通用规则

1. **文件夹/文件名全 ASCII**（`Chinese5000`，不用中文名）；文件名 = 类名，PascalCase；
2. 脚本进 `Scripts/`，资产进 `Data/`，预制体进 `Prefabs/`——不混放；
3. 新增脚本先补《死锁标记》再加《程序分工表》认领，然后动笔；
4. `Scripts/Editor/` 只放 Editor 程序集代码（`UnityEditor` 命名空间），被运行时引用即打回；
5. SO 资产由编辑器导入器生成（`Data/`），手改资产值可以、手改结构不行（结构 = Core/ 里的 SO 类型，归逻辑层）；
6. 场景文件视同脚本占锁；删 `SampleScene.unity` 前群里说一声。

---

## 6. 快速自检（提交前）

- [ ] 文件在正确的文件夹里（§3 对照表）
- [ ] 没闯别人 ✅ 的文件夹（§2 矩阵）
- [ ] 新脚本已补死锁标记 + 分工表
- [ ] 命名与 §4 对齐表一致，无中文文件名
- [ ] 禁改区（Packages/ProjectSettings/Library/Temp/obj/csproj）零改动
