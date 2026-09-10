# ERA Basic 开发者技能包

为 Gemini CLI、Claude Code、Codex 等 AI 编程助手准备的 ERA Basic（ERB）离线知识库。仓库本身就是可安装的 Agent Skill：根目录包含 `SKILL.md`，完整参考文档位于 `references/`。

![References](https://img.shields.io/badge/references-37-7c3aed?style=flat-square)

> 面向 Emuera.NET，覆盖 ERB 语法、变量体系、命令与函数 API、系统流程、CSV/ERH、DataTable/MAP/XML、图形、音频、资源、配置与调试。

## 简介

技能包收录 Emuera 的 ERB 语法、变量、命令和系统机制，供 Gemini CLI、Claude Code、Codex 等 AI 编程助手进行快速查询。

## 核心特性

- **离线可检索**：资料全部保存在仓库内，不依赖联网检索或模型记忆。
- **读取思路**：设计上是假设智能体先读取 `SKILL.md`，随后根据具体要求在 `TASK_MAP.md` 和 `INDEX.md` 确认需要的内容，再找到具体的相关内容描述。
- **精确到文档**：API 签名、参数和限制以 `references/` 中的参考文档为准。

## 仓库结构

```text
.
├── SKILL.md                 # Agent Skill 入口、路由策略和速查表
├── README.md                # 项目说明与文档地图
├── LICENSE                  # GPL-2.0
└── references/
    ├── TASK_MAP.md          # 任务导航
    ├── INDEX.md             # 全库文档索引
    ├── core-concepts/       # 语法、变量、表达式、函数、预处理器
    ├── commands/            # 命令与函数 API 分类参考
    ├── system-flow/         # 游戏系统流程
    ├── csv-reference/       # CSV 与 ERB/ERH 文件格式
    ├── game-config/         # 运行配置与图片资源
    ├── getting-started/     # Emuera 使用方法
    ├── version-diff/        # Emuera 与旧版差异
    └── reference/           # 快捷键与调试参考
```

## 安装方法

> 直接把Github连接给编程工具（比如Codex），让它安装。  
> 或者下载后把压缩包或文件夹给它。

## 检索流程

```text
SKILL.md
  ├─ 分类问题，查看核心速查
  ├─ 不确定时进入 references/TASK_MAP.md
  ├─ 由 references/INDEX.md 精确定位文件
  └─ 只打开 1-3 个目标文档获取细节
```

| 入口 | 作用 |
| --- | --- |
| [`SKILL.md`](./SKILL.md) | Skill 元数据、路由策略、核心语法和命令速查 |
| [`references/TASK_MAP.md`](./references/TASK_MAP.md) | 按开发任务反查相关文档 |
| [`references/INDEX.md`](./references/INDEX.md) | 按分类浏览全部参考文档路径与摘要 |

`references/` 当前包含 **37 个 Markdown 文件**，其中 35 个专题文档、2 个导航文件。

## 文档列表

### 核心概念

| 文档 | 内容 |
| --- | --- |
| [`syntax-basics.md`](./references/core-concepts/syntax-basics.md) | ERB 基础语法、文件组织、变量、赋值、函数、条件与循环 |
| [`expressions.md`](./references/core-concepts/expressions.md) | Emuera 扩展表达式、行尾注释、行连接、FORM 与字符串表达式 |
| [`operators.md`](./references/core-concepts/operators.md) | 运算符、优先级、算术、比较、逻辑、位运算与三目运算 |
| [`variables.md`](./references/core-concepts/variables.md) | 内置变量、CSV 引用变量、角色变量、局部变量与全局变量 |
| [`user-defined-variables.md`](./references/core-concepts/user-defined-variables.md) | `#DIM`/`#DIMS` 及 `DYNAMIC`、`CONST`、`REF`、`SAVEDATA` 等属性 |
| [`header-files.md`](./references/core-concepts/header-files.md) | ERH 头文件、全局变量定义、`SAVEDATA`/`CHARADATA`/`GLOBAL` |
| [`preprocessor.md`](./references/core-concepts/preprocessor.md) | 预处理器、宏定义、参数声明和函数属性 |
| [`user-defined-functions.md`](./references/core-concepts/user-defined-functions.md) | 一般函数、式中函数、参数传递、引用传递与事件函数属性 |
| [`in-expression-functions.md`](./references/core-concepts/in-expression-functions.md) | 内置式中函数一览与使用限制 |

### 命令参考

| 文档 | 内容 |
| --- | --- |
| [`print-system.md`](./references/commands/print-system.md) | `PRINT`/`PRINTV`/`PRINTS`/`PRINTFORM`/`PRINTDATA` 等输出命令 |
| [`display-font.md`](./references/commands/display-font.md) | 颜色、字体、对齐、重绘、进度条和跳过显示 |
| [`control-flow.md`](./references/commands/control-flow.md) | `IF`/`SIF`/`FOR`/`WHILE`/`REPEAT`/`SELECTCASE`/`TRY` 等 |
| [`input.md`](./references/commands/input.md) | `INPUT`/`INPUTS`/`ONEINPUT`/`TINPUT`/`INPUTMOUSEKEY` 等输入命令 |
| [`character.md`](./references/commands/character.md) | 角色添加、删除、复制、查找、排序与拾取 |
| [`data-save-load.md`](./references/commands/data-save-load.md) | 游戏、角色、全局数据和文本的存档读档 |
| [`system.md`](./references/commands/system.md) | `BEGIN`/`QUIT`、时间、内存、配置和窗口控制 |
| [`string-operations.md`](./references/commands/string-operations.md) | 大小写、全半角、子串、查找、分割、匹配与替换 |
| [`math-etc.md`](./references/commands/math-etc.md) | 数学、数组操作、变量参照、文件检查、`SWAP`/`TIMES` |
| [`data-table.md`](./references/commands/data-table.md) | DataTable 创建、列行操作、单元格查询与 XML 序列化 |
| [`map.md`](./references/commands/map.md) | MAP 关联数组的创建、查询、修改、遍历与序列化 |
| [`xml.md`](./references/commands/xml.md) | XML 文档、节点、属性和 XPath 查询 |
| [`graphics.md`](./references/commands/graphics.md) | 画布、图形、文字、精灵、动画和按钮背景 |
| [`sound.md`](./references/commands/sound.md) | 音效与 BGM 播放、停止、音量和文件检查 |
| [`html-print.md`](./references/commands/html-print.md) | `HTML_PRINT` 标签系统、转义和纯文本转换 |
| [`tooltip.md`](./references/commands/tooltip.md) | 工具提示延迟、时长、颜色、字体和格式 |

### 流程、数据与运行

| 文档 | 内容 |
| --- | --- |
| [`system-flow.md`](./references/system-flow/system-flow.md) | TITLE → FIRST → SHOP → TRAIN 流程与系统函数 |
| [`config.md`](./references/game-config/config.md) | `emuera.config`、`_replace.csv` 和 `_Rename.csv` |
| [`resources.md`](./references/game-config/resources.md) | 图片资源目录、资源 CSV、精灵表、动画精灵与绘图接口 |
| [`csv-format.md`](./references/csv-reference/csv-format.md) | `chara*.csv`、`item.csv`、`abl.csv`、`str.csv` 等格式 |
| [`erb-format.md`](./references/csv-reference/erb-format.md) | ERB/ERH 编码、命名规则、跳过区块与文件组织实践 |
| [`differences.md`](./references/version-diff/differences.md) | Emuera 与旧版 Emuera、eramaker 的功能与兼容性差异 |
| [`usage.md`](./references/getting-started/usage.md) | 运行环境、基本操作、键盘宏、菜单和 `_Rename.csv` |
| [`shortcuts.md`](./references/reference/shortcuts.md) | Emuera 用户界面快捷键 |
| [`debug.md`](./references/reference/debug.md) | 调试模式与调试功能，仅供排查复杂问题 |
| [`glossary.md`](./references/glossary.md) | 启动模式、窗口、函数、行、表达式和变量术语 |

## 快速检索

在仓库根目录可以使用 `rg` 检索：

```bash
# 搜索命令名称
rg -n "SAVEGAME|LOADGAME" references

# 查看某类命令的所有定义
rg -n "^##|^###" references/commands/tooltip.md

# 查找变量或关键字
rg -n "CFLAG|TALENT|EXP" references/core-concepts/variables.md
```

## 其它说明

- 当前版本以 [EmueraEM+EE 文档仓库](https://gitlab.com/EvilMask/emuera.em.doc) 的`5c1ed0dcdede40a7f435c6cdb16f578dd784c834`提交为基础制作
