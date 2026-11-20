# 宇宙银河机甲 · UrhoX 版 v1.4.3

UrhoX / TapTap 制造 版本的修复包（v1.4.3）。内容原样取自 `GalaxyMecha_UrhoX_Release_v1_4_3.zip`，只把内层 `UrhoX_Release/` 摊平到仓库根目录，未修改任何代码或资源。

本次是**纯修复版**：只改 UrhoX 版手写的界面 / 渲染代码，战斗逻辑（`CosmicRun.lua` / `CosmicData.lua`）与 v1.4.2、网页版 21 相同。

## 版本与结构

- 版本号以 `VERSION` 为准：**1.4.3**
- 文件 194 个，合计约 3.1 MB（本包不含 `store/` 上架素材里的宣传片与截图）

```
.project/                 UrhoX 工程配置
VERSION                   1.4.3
assets/Sounds/            19 个音效与 BGM（.ogg）
docs/                     12 篇文档（含 更新说明 v1.3.6 / v1.4.0 / v1.4.1 / v1.4.2 / v1.4.3 等）
scripts/
  main.lua                引擎入口
  cosmic/                 13 个界面与流程脚本（本次修 GameUI.lua）
  game/                   1 个（VoxelBuilder.lua，本次修特效材质）
  jsrt/                   7 个运行时（本次修 Renderer.lua 的原型颜色上限）
  voxeldata/              3 + models/ 123 个体素模型
tools/xpile_lite/         网页版 -> Lua 转换器，含 src_web21/
网页版19源码_供转换器/      19 版网页版 JS 源码
```

## v1.4.3 修复内容（摘自 `docs/更新说明_v1.4.3.md`）

**1. 点升级卡牌后被弹回机库（v1.4.2 引入）**
- 现象：重播新手引导后第一次升级，以及任何时候点卡牌、点“跳过”，界面直接回到机库
- 原因：`GameUI.lua` 点卡牌后立刻关闭弹窗（`u.levelup = nil`），同一帧后段“稍后选择”按钮仍在读 `u.levelup.forced`，Lua 报错，`main.lua` 的保护逻辑捕获后保存存档并回机库
- 修复：弹窗开头记录当前状态，点卡牌 / 跳过后本帧直接结束绘制；看广告刷新回调先判断弹窗是否还在

**2. 敌人与机甲攻击特效显示成白色**
- 原因 1：特效材质是“受光 + 自发光（颜色 ×1.6~3.5）”，网页版在 HDR 后做色调映射，UrhoX 直接输出 LDR，三通道被截到 1，青 / 红都变白。修复：`VoxelBuilder.Build` 对 `fx_` 开头带自发光的模型改用“不受光 + 最大通道为 1”的发光材质，保留色相并仍触发辉光
- 原因 2：`Renderer.lua` 每种“模型 + 颜色”建一个原型，同一模型超过 120 种颜色后退回无色模型；尾焰、爆炸余烬颜色渐变，`fx_white` 很快超限。修复：特效颜色按最大通道归一再量化（变体数约减半），上限提到 160，超限时借用最接近的已有颜色

**测试**（Lua 5.4 无头）
- 界面流程：老存档重播引导 → 首次升级强制弹出并点卡牌 → HUD 升级按钮 → 暂停 → 设置 → 改伤害数字 → 返回 → 继续 → 拾取宝箱 → 结算，全程无报错（v1.4.2 会在第一次点卡牌时报错）
- 渲染后端：第 3 章、危险 3 跑完整章，`fx_white` 颜色变体 75 个，无退回无色模型的情况；全部 Lua 文件编译通过

## 怎么运行

1. 以仓库根目录作为 UrhoX 项目，入口 `scripts/main.lua`；
2. 音频、模型路径均相对根目录，请保留原目录结构；
3. 上架流程见 `docs/TapTap制造接入指南.md` 与 `docs/提审清单.md`。

## 使用前请注意

- 尚未在真实 UrhoX / TapTap 制造真机验证。本次需要真机确认：弹道颜色（玩家激光青色、敌人子弹红色、爆炸橙色，白色只保留在弹芯与白热火花）；若特效偏暗或辉光不足，可在设置里把“辉光”调到“强”。
- `docs/用户协议模板.md`、`docs/隐私政策模板.md` 为模板，上线前需按运营主体与法务意见修改。
- 本包不含网页版 HTML；对应网页版为测试版 21，见 `GalaxyMecha-UrhoX-Release-v1.4.2` 仓库的 `web/` 目录。

## 相关仓库

- 上一版（v1.4.2，含网页版 21）：`https://github.com/mmb9w48ff7/GalaxyMecha-UrhoX-Release-v1.4.2`
- 更早版本：`GalaxyMecha-UrhoX-Release-v1.4.1`、`GalaxyMecha-UrhoX-Release-v1.3.5`
- 源码工程（作者专用）：`https://github.com/mmb9w48ff7/GalaxyMecha-Source-v1.3.5`
- 网页版合集：`https://github.com/mmb9w48ff7/GalaxyMecha-Web-2`
