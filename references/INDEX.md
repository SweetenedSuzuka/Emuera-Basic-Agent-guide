# 参考文档索引 (INDEX)

本文件列出 `references/` 下所有参考文档的路径与简要说明。

> **路径说明**：以下路径以 `references/` 为前缀，相对于本 skill 目录（`emuera-basic/`）。从 skill 根目录可直接使用；若从 INDEX.md 所在目录（`references/`）出发，去掉 `references/` 前缀即可。

## 核心概念 (core-concepts/)

| 文件 | 说明 |
|------|------|
| `references/core-concepts/syntax-basics.md` | ERB 基础语法：注释、变量、赋值、数组、函数定义、IF/FOR/WHILE 等 |
| `references/core-concepts/variables.md` | 完整变量体系：内置变量、CSV 引用变量、角色变量、局部/全局变量等 |
| `references/core-concepts/expressions.md` | Emuera 扩展表达式：行尾注释、行连接、;!;特殊注释、FORM 语法、字符串表达式等 |
| `references/core-concepts/operators.md` | 运算符一览：优先级、算术、比较、逻辑、位运算、三目运算 |
| `references/core-concepts/user-defined-variables.md` | 用户定义变量：#DIM/#DIMS、DYNAMIC、CONST、REF、SAVEDATA、CHARADATA、GLOBAL |
| `references/core-concepts/in-expression-functions.md` | 式中函数（内联函数）：内置函数一览与使用方法 |
| `references/core-concepts/header-files.md` | ERH 头文件：全局变量定义、SAVEDATA/CHARADATA/GLOBAL 关键字 |
| `references/core-concepts/preprocessor.md` | 函数定义、参数传递、宏定义(#DEFINE)、系统函数一览 |

## 命令参考 (commands/)

| 文件 | 说明 |
|------|------|
| `references/commands/print-system.md` | PRINT 系命令：PRINT/PRINTV/PRINTS/PRINTFORM/PRINTDATA/DRAWLINE/PRINT_IMG 等 |
| `references/commands/display-font.md` | 显示操作：SETCOLOR/SETFONT/ALIGNMENT/REDRAW/BAR/SKIPDISP 等 |
| `references/commands/string-operations.md` | 字符串操作：TOUPPER/TOSTR/STRLEN/SUBSTRING/STRFIND/SPLIT/MATCH 等 |
| `references/commands/control-flow.md` | 流程控制：IF/SIF/FOR/WHILE/REPEAT/SELECTCASE/GOTO/CALL/JUMP/TRY/THROW 等 |
| `references/commands/input.md` | 输入命令：INPUT/INPUTS/ONEINPUT/TINPUT/TONEINPUT/INPUTMOUSEKEY 等 |
| `references/commands/system.md` | 系统命令：BEGIN/QUIT/存档/时间/内存/配置等 |
| `references/commands/character.md` | 角色操作：ADDCHARA/DELCHARA/COPYCHARA/FINDCHARA/PICKUPCHARA/SORTCHARA 等 |
| `references/commands/data-save-load.md` | 存档读档：SAVEGAME/LOADGAME/SAVEDATA/LOADDATA/SAVEGLOBAL/LOADGLOBAL/CHKDATA 等 |
| `references/commands/data-table.md` | DataTable 命令：DT_CREATE/DT_COLUMN_ADD/DT_ROW_ADD/DT_CELL_GET/DT_SELECT 等 |
| `references/commands/map.md` | MAP（关联数组）命令：MAP_CREATE/MAP_SET/MAP_GET/MAP_REMOVE/MAP_GETKEYS 等 |
| `references/commands/xml.md` | XML 命令：XML_DOCUMENT/XML_GET/XML_SET/XML_ADDNODE/XML_TOSTR 等 |
| `references/commands/graphics.md` | 图形命令：GCREATE/GDRAWG/GDRAWTEXT/SPRITECREATE/CBGSETG/SETBGIMAGE 等 |
| `references/commands/sound.md` | 音频命令：PLAYSOUND/STOPSOUND/PLAYBGM/STOPBGM/SETSOUNDVOLUME 等 |
| `references/commands/html-print.md` | HTML_PRINT 标签系统：p/nobr/button/font/img/div 等标签、HTML_ESCAPE 等函数 |
| `references/commands/math-etc.md` | 数学/数组/变量参照：RAND/ABS/MAX/MIN/ARRAYSORT/SUMARRAY/VARSIZE/EXISTFILE 等 |

## 系统流程 (system-flow/)

| 文件 | 说明 |
|------|------|
| `references/system-flow/system-flow.md` | 游戏系统流程：TITLE→FIRST→SHOP→TRAIN 各阶段的详细说明，系统函数一览 |

## 游戏配置 (game-config/)

| 文件 | 说明 |
|------|------|
| `references/game-config/config.md` | emuera.config 配置项、_replace.csv 可替换项目、_Rename.csv |

## 与 eramaker 差异 (eramaker-diff/)

| 文件 | 说明 |
|------|------|
| `references/eramaker-diff/differences.md` | Emuera 与 eramaker 的所有差异：修复的 Bug、新增功能、兼容性注意事项 |

## CSV 参考 (csv-reference/)

| 文件 | 说明 |
|------|------|
| `references/csv-reference/csv-format.md` | CSV 文件格式：chara*.csv、item.csv、abl.csv、str.csv 等完整说明 |
| `references/csv-reference/erb-format.md` | ERB/ERH 文件格式：编码、命名规则、[SKIPSTART]/[SKIPEND]、最佳实践 |

## 其他参考 (reference/)

| 文件 | 说明 |
|------|------|
| `references/reference/shortcuts.md` | Emuera 快捷键一览（用户操作参考，非代码编写） |
| `references/reference/debug.md` | 调试功能完整参考（**不推荐在开发中使用**） |

## 导航

| 文件 | 说明 |
|------|------|
| `references/TASK_MAP.md` | 按任务导航：从"我想做 X"定位到对应文档 |

## 快速查找命令

```bash
# 按关键词命中文件
rg -n "PRINT" references/INDEX.md
rg -n "角色\|キャラ" references/INDEX.md

# 查找特定命令
rg -n "SAVEGAME\|LOADGAME" references/commands/

# 查找变量
rg -n "FLAG\|CFLAG" references/core-concepts/variables.md
```
