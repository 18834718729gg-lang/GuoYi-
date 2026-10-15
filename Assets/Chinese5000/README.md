# Chinese5000 — 工程结构与分工规则

本文件夹是《中国五千年》全部游戏代码与资产的根。

**权威规则文档在工程根目录：`D:/unity项目/Chinese/README_项目结构与分工.md`**
（文件夹结构 / Ownership 矩阵 / 脚本归属对照 / 命名对齐 / 待补录清单 —— 动代码前必读）

速查：
- `Scripts/Bootstrap` `Core` `Systems` `Data` `Save` → **逻辑层**负责
- `Scripts/UI` + `Prefabs/` → **表现层**负责
- `Scripts/Editor` + `Data/` 资产 → **编辑器**负责（数值策划在 Inspector 填资产值）
- 场景：`Assets/Scenes/Game.unity`（持久）+ `Museum3D.unity` / `Tree2D.unity`（additive）

配套桌面文档：`C:\Users\IT\Desktop\比赛策划案\` 下架构拆解四件套 + 程序分工表 + 死锁标记。
