# 🎮 ERA Basic 开发者技能包

给 Gemini CLI、Claude Code、Codex 等 AI 编程助手使用的 ERA Basic（ERB）离线参考技能库。

![docs](https://img.shields.io/badge/docs-30%20markdown%20files-7c3aed?style=flat-square)

> 面向 Emuera（EmueraEM+EE）的本地知识源，覆盖 ERB 语法、变量体系、180+ 命令 API、系统流程、CSV/DataTable/MAP/XML 数据处理、图形与音频。

## 🎯 解决的问题

AI 编程助手在 ERA Basic 开发中经常碰到的几类问题：

- 不确定 ERB 的确切语法规则——ERB 是一套不同于主流语言的领域脚本，模型没有可靠的训练数据
- 不知道某个命令是否存在、参数是什么、返回值怎么取
- 变量体系庞大（100+ 内置变量，7 种类别），记忆负担重且容易混淆
- 游戏系统流程（TITLE→FIRST→SHOP→TRAIN）和事件函数（@EVENTFIRST、@SHOW_USERCOM 等）难以理解
- Emuera 与旧版 eramaker 的差异不清楚，不知道哪些功能可以直接用

本仓库把这些不确定性变成**可定位、可查阅、可验证的本地文件查询**。

## Before / After

**没有 skill**：模型凭记忆猜 ERB 命令名、参数格式和语法，输出看起来合理但经常是编造的——参数数量不对、API 签名错误、功能描述完全相反。

**使用本 skill**：先按 `SKILL.md → TASK_MAP.md → INDEX.md` 命中文档路径，再打开目标 Markdown，最后给出精确的命令签名和 ERB 代码示例。

## ✨ 核心特性

- **完全离线检索**：不依赖模型记忆，先命中文档路径再读取正文
- **为 Agent 工作流设计**：按 `SKILL.md → TASK_MAP → INDEX` 层层递进检索
- **日文原档为基准**：基于 [EmueraEM+EE 日语文档](https://gitlab.com/EvilMask/emuera.em.doc) 编写，避免翻译滞后和机翻错误
- **中文全文书写**：所有描述性文本使用中文，代码块和引擎关键字保留原文
- **命令签名精确**：每个命令的参数数量、类型、顺序均对照原档核实
- **默认面向 Emuera**：不考虑旧版 eramaker 兼容，除非用户明确要求

## 📚 内容导览

| 入口 / 模块 | 用途 |
| --- | --- |
| [`SKILL.md`](./emuera-basic/SKILL.md) | 技能规则唯一来源：路由策略、核心速查、命令速查表、回答约束 |
| [`references/TASK_MAP.md`](./emuera-basic/references/TASK_MAP.md) | 按任务反查（"我想做 X"→ 对应文档） |
| [`references/INDEX.md`](./emuera-basic/references/INDEX.md) | 全库文件索引（28 个参考文档路径） |

### 核心概念

| 文件 | 说明 |
| --- | --- |
| [`syntax-basics.md`](./emuera-basic/references/core-concepts/syntax-basics.md) | ERB 基础语法：注释、变量、赋值、数组、函数定义 |
| [`variables.md`](./emuera-basic/references/core-concepts/variables.md) | 完整变量体系：100+ 内置变量按类别详述 |
| [`expressions.md`](./emuera-basic/references/core-concepts/expressions.md) | Emuera 扩展表达式：行尾注释、行连接、FORM 语法、字符串式 |
| [`operators.md`](./emuera-basic/references/core-concepts/operators.md) | 运算符一览：优先级、算术、比较、逻辑、位运算、三目运算 |
| [`user-defined-variables.md`](./emuera-basic/references/core-concepts/user-defined-variables.md) | 用户定义变量：#DIM/#DIMS 及其所有变体 |
| [`in-expression-functions.md`](./emuera-basic/references/core-concepts/in-expression-functions.md) | 式中函数（内联函数）：内置函数一览与使用方法 |
| [`header-files.md`](./emuera-basic/references/core-concepts/header-files.md) | ERH 头文件：全局变量声明、SAVEDATA/CHARADATA/GLOBAL 关键字 |
| [`preprocessor.md`](./emuera-basic/references/core-concepts/preprocessor.md) | 函数定义、参数传递、宏定义(#DEFINE)、预处理器指令一览 |

### 命令参考

| 文件 | 说明 |
| --- | --- |
| [`print-system.md`](./emuera-basic/references/commands/print-system.md) | PRINT 系命令大全 |
| [`display-font.md`](./emuera-basic/references/commands/display-font.md) | 颜色、字体、对齐、进度条、跳过控制 |
| [`string-operations.md`](./emuera-basic/references/commands/string-operations.md) | 字符串操作：大小写、全半角、子串、查找、分割、匹配 |
| [`control-flow.md`](./emuera-basic/references/commands/control-flow.md) | 流程控制：IF/SIF/FOR/WHILE/REPEAT/SELECTCASE/GOTO/CALL/TRY |
| [`input.md`](./emuera-basic/references/commands/input.md) | 输入命令：INPUT/INPUTS/ONEINPUT/TINPUT/INPUTMOUSEKEY |
| [`system.md`](./emuera-basic/references/commands/system.md) | 系统命令：BEGIN/QUIT/存档/时间/内存/配置/调试 |
| [`character.md`](./emuera-basic/references/commands/character.md) | 角色操作：ADDCHARA/DELCHARA/FINDCHARA/PICKUPCHARA/SORTCHARA |
| [`data-save-load.md`](./emuera-basic/references/commands/data-save-load.md) | 存档读档：SAVEGAME/SAVEDATA/SAVECHARA/SAVETEXT/CHKDATA |
| [`data-table.md`](./emuera-basic/references/commands/data-table.md) | DataTable 数据库命令 |
| [`map.md`](./emuera-basic/references/commands/map.md) | MAP（关联数组）命令 |
| [`xml.md`](./emuera-basic/references/commands/xml.md) | XML 文档处理命令 |
| [`graphics.md`](./emuera-basic/references/commands/graphics.md) | 图形：画布、精灵、动画、按钮背景 |
| [`sound.md`](./emuera-basic/references/commands/sound.md) | 音频：音效/BGM 播放与音量控制 |
| [`math-etc.md`](./emuera-basic/references/commands/math-etc.md) | 数学、数组操作、变量参照、文件检查 |

### 专题文档

| 文件 | 说明 |
| --- | --- |
| [`system-flow.md`](./emuera-basic/references/system-flow/system-flow.md) | 游戏系统流程：TITLE→FIRST→SHOP→TRAIN 各阶段详解 |
| [`config.md`](./emuera-basic/references/game-config/config.md) | 游戏配置：emuera.config、_replace.csv、VariableSize.csv、调试模式 |
| [`differences.md`](./emuera-basic/references/eramaker-diff/differences.md) | Emuera 与 eramaker 的所有差异 |
| [`csv-format.md`](./emuera-basic/references/csv-reference/csv-format.md) | CSV 文件格式：chara*.csv、item.csv、str.csv 等完整说明 |
| [`erb-format.md`](./emuera-basic/references/csv-reference/erb-format.md) | ERB/ERH 文件格式规范与最佳实践 |

## 🧭 推荐检索路径

```text
SKILL.md → TASK_MAP.md → INDEX.md → 目标 Markdown
```

设计原则：先从 SKILL.md 的核心速查表和命令速查表找答案；需要精确签名或细节时，通过 TASK_MAP 定位主题，INDEX 命中文件路径，最后只打开 1-3 个文件读细节。

## 📦 适用场景

- **ERB 语法查询**：注释、变量赋值、数组访问、表达式、运算符优先级
- **命令 API 确认**：PRINT 系、控制流、输入、角色操作、存档读档等 180+ 命令的精确签名
- **新增功能开发**：DataTable（数据库）、MAP（关联数组）、XML 处理、图形/精灵、音频播放
- **游戏系统架构**：理解 @EVENTFIRST / @SHOP / @TRAIN 等事件函数的调用时机和职责
- **CSV 数据配置**：chara*.csv、item.csv、str.csv、VariableSize.csv 等文件的格式和用法
- **Agent 集成**：作为 Gemini CLI、Claude Code、Codex 的 ERA Basic 知识检索层

## 🚀 快速接入

将 `emuera-basic/` 目录放入 AI 编程助手的 skills 扫描路径：

```bash
# Claude Code / Codex
cp -r emuera-basic ~/.agents/skills/

# 或使用 skills CLI
npx skills add <your-repo-url> --skill emuera-basic
```

## 📜 来源与许可

- 数据源：[EmueraEM+EE 文档仓库](https://gitlab.com/EvilMask/emuera.em.doc)（Emuera 引擎官方文档）
- 当前版本以 EmueraEM+EE 文档仓库的`5c1ed0dcdede40a7f435c6cdb16f578dd784c834`提交为基础制作
