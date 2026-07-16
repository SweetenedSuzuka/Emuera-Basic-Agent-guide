---
name: emuera-basic-agent-guide
description: 用于 ERA Basic（ERB）脚本的开发帮助。覆盖 Emuera 的 ERB 语法、变量体系、命令/函数 API、系统流程、角色与 CSV 数据处理、图形/音频、DataTable/MAP/XML、调试与配置。默认面向 Emuera，不考虑旧版 eramaker 兼容。
metadata:
  version: "1.0.0"
---

# ERA Basic（ERB）开发指南

使用本技能来回答 ERA Basic / Emuera 脚本开发相关的问题。核心知识包含在本文档中，详细 API 签名和参数请按路由打开 `references/` 下的具体文件。

本文件内的路径如 `references/...` 均相对于此 skill 目录（`emuera-basic/`）。

## 版本

当前 skill 版本：`v1.0.0`。

参考快照：基于 EmueraEM+EE 的[日语文档](https://gitlab.com/EvilMask/emuera.em.doc)，本文档中所有命令和语法均以 Emuera 为准。除非用户明确表示目标引擎是旧版 eramaker，否则直接使用 Emuera 的全部功能即可，不需要考虑兼容性。

## 路由策略

收到 ERA Basic 开发问题后，按以下层级定位信息：

1. **分类请求**
   - **基础语法**（注释、变量定义、赋值、表达式、运算符）：本文档「核心概念速查」章节已覆盖，如需要更多细节打开 `references/core-concepts/` 下对应文件。
   - **命令/函数 API**（PRINT 系、IF/FOR/WHILE、CALL/JUMP、角色操作、存档等）：先查本文档「核心概念速查」中的命令速查表，再按需打开 `references/commands/` 下对应分类文件。
   - **系统流程**（@EVENTFIRST、@SHOW_SHOP、@COM 等系统函数）：打开 `references/system-flow/system-flow.md`。
   - **游戏配置**（emuera.config、_replace.csv、VariableSize.csv）：打开 `references/game-config/` 下对应文件。
   - **CSV 格式 / ERB 文件格式**：打开 `references/csv-reference/` 下对应文件。
   - **如果必须兼容旧版 eramaker**：打开 `references/eramaker-diff/differences.md`（仅当用户明确要求兼容 eramaker 时才需要）。
   - **不确定领域**：先查 `references/TASK_MAP.md`，再通过 `references/INDEX.md` 精确定位。

2. **选择最小索引**
   - 按任务导航：`references/TASK_MAP.md`
   - 全库路径清单：`references/INDEX.md`

3. **打开目标文件**
   - `references/commands/` 下按功能分类（print-system.md、control-flow.md、character.md 等）
   - `references/core-concepts/` 下按主题分类（variables.md、expressions.md、operators.md 等）

4. **只打开需要的文件**，不要全量读取。

## 核心概念速查

### 什么是 ERA Basic？

ERA Basic（ERB）是一种运行在 Emuera 引擎上的领域特定脚本语言，用于制作文字调教类游戏。

本技能参考 Emuera 的[官方日语文档](https://evilmask.gitlab.io/emuera.em.doc/) 编写。Emuera 是 eramaker 的超集，除非用户明确说明目标是旧版 eramaker，否则按 Emuera 编码，无需考虑兼容性。

- **文件扩展名**：`.ERB`（脚本文件）、`.ERH`（头文件）
- **引擎**：Emuera（.NET 实现，EM+EE 版需 .NET 7.0+）
- **关键概念**：角色系统、CSV 数据驱动、回合制游戏循环

### 基本语法

```
; 单行注释（分号开头）
;!; Emuera 中视为有效代码，eramaker 中注释掉
;#; 仅在调试模式下执行

; 变量赋值
MONEY = 500
A = B + C * 2
STR:0 = 示例字符串

; 字符串变量赋值（字符串表达式）
STR '= "字符串"

; 数组访问
FLAG:0 = 1
CFLAG:MASTER:10 = 5

; 行连接（用 { } 括起来的多行视为一行）
{
    #DIM CONST HOGE =
        1,2,3,4
}
```

### 变量体系

ERA Basic 有丰富的内建变量：

| 类别 | 示例 | 说明 |
|------|------|------|
| 单字母变量 | `A`～`Z` | 整数类型一维数组，可保存 |
| 基础游戏变量 | `MONEY`、`DAY`、`TIME` | 游戏状态 |
| 角色变量 | `TARGET`、`MASTER`、`ASSI`、`PLAYER` | 角色操作 |
| 命令相关 | `SELECTCOM`、`PREVCOM`、`NEXTCOM` | 调教命令 |
| 状态变量 | `FLAG`、`TFLAG`、`CFLAG` | 整数类型标志 |
| 字符串变量 | `STR`、`SAVESTR`、`TSTR`、`CSTR` | 字符串存储 |
| 角色属性 | `BASE`、`ABL`、`TALENT`、`EXP`、`MARK`、`PALAM`、`STAIN`、`EX`、`SOURCE` | 角色参数 |
| 局部变量 | `LOCAL`、`LOCALS`、`ARG`、`ARGS` | 函数内局部 |
| 全局变量 | `GLOBAL`、`GLOBALS` | 跨存档共享 |
| CSV 变量 | `ITEMNAME`、`TALENTNAME` 等 | CSV 定义值引用 |
| 用户定义 | `#DIM`、`#DIMS` 定义 | 自定义变量 |

### 数组访问

```
; 一维数组
FLAG:0 = 1

; 二维数组（角色变量）
CFLAG:MASTER:10 = 5

; 三维数组
CDFLAG:MASTER:0:2

; 字符串形式索引
CFLAG:"角色名":flag_no
```

### 运算符优先级

从高到低：

1. `~`、`!` — 位取反、逻辑非（单目）
2. `*`、`/`、`%` — 乘除取模
3. `+`、`-` — 加减
4. `<<`、`>>` — 位移
5. `<`、`>`、`<=`、`>=` — 比较
6. `==`、`!=` — 等值比较（支持字符串）
7. `&`、`|`、`^` — 位与/或/异或
8. `&&`、`!&`、`||`、`!|`、`^^` — 逻辑与/与非/或/或非/异或
9. `?～#` — 三目运算符

### 控制流

```
; IF 语句
IF condition
    ; 真
ELSEIF other_condition
    ; 
ELSE
    ;
ENDIF

; SIF（单行 IF）
SIF condition
    PRINT 条件成立

; FOR 循环
FOR LCOUNT, 0, CHARANUM
    ; LCOUNT 从 0 到 CHARANUM
NEXT

; WHILE 循环
WHILE condition
    ;
WEND

; REPEAT 循环
REPEAT 10
    ; COUNT:0 为当前计数
REND

; SELECTCASE
SELECTCASE expression
CASE 0
    ;
CASE 1
    ;
CASEELSE
    ;
ENDSELECT

; 跳转
GOTO label
$label
    ;

; 函数调用
CALL function_name, arg0, arg1
JUMP function_name
RETURN
```

### 函数定义

```
; 事件函数（可多重定义）
@EVENTFIRST
    ; 游戏开始时调用
    PRINTL 游戏开始！

; 一般函数
@FUNC_NAME
    ; 处理
    RETURN

; 带参数的函数
@FUNC_NAME, ARG:0, ARGS:0
    ; ARG:0 是数值参数，ARGS:0 是字符串参数
    RETURN RESULT

; 调用
CALL FUNC_NAME, 123, "字符串"
```

### 用户定义变量

`#DIM` 定义整数变量，`#DIMS` 定义字符串变量。可加 `CONST`（常量）、`DYNAMIC`（动态）、`REF`（引用）修饰。只能写在 `@函数名` 声明下一行（局部）或 `.ERH` 中（全局）。

```
; 函数内私有变量
@FUNC_NAME
#DIM MY_INT, 100        ; 整数类型数组，100 个元素
#DIMS MY_STR, 50        ; 字符串类型数组，50 个元素
#DIM CONST HOGE = 1,2,3 ; 常量（需初始化，定义后不可修改）
#DIM DYNAMIC DYN_VAR    ; 动态变量（函数调用时分配，结束时释放）
#DIM REF REF_VAR        ; 引用类型变量（指向另一个变量）

; ERH 中的全局变量
#DIM MY_GLOBAL_INT, 100
#DIM SAVEDATA MY_SAVED  ; 可保存的全局变量
#DIM CHARADATA C_VAR, 50 ; 角色变量
#DIM GLOBAL G_VAR, 100   ; 跨存档全局变量
```

### 宏定义

`#DEFINE` 在预处理阶段将宏名替换为宏内容（文本替换）。只能写在 `.ERH` 中。

```
; ERH 中定义宏
#DEFINE MACRO_NAME
    ; 宏内容
#ENDDEFINE

; 带参数的宏
#DEFINE MACRO_NAME(arg1, arg2)
    ; 使用 arg1, arg2
#ENDDEFINE
```

### 式中函数（内联函数）

```
; 使用内建式中函数
A = ABS(A)
IF STRLENS(STR:0) > 10

; 用户定义式中函数
@FUNC_NAME(value)
#FUNCTION
    ; 处理
    RETURNF result
```

## 命令速查表

### PRINT 系

| 命令 | 说明 |
|------|------|
| `PRINT` / `PRINTV` / `PRINTS` / `PRINTFORM` / `PRINTFORMS` | 基础打印（可加 L/W/K/D 后缀） |
| `PRINTC` / `PRINTLC` | 居中打印 |
| `PRINTDATA` | 打印数据 |
| `PRINTBUTTON` | 打印按钮 |
| `PRINTPLAIN` | 纯文本打印 |
| `DRAWLINE` / `CUSTOMDRAWLINE` | 打印分隔线 |
| `PRINT_IMG` / `PRINT_RECT` / `PRINT_SPACE` | 图像/矩形/空格打印 |

### 流程控制

| 命令 | 说明 |
|------|------|
| `IF` / `ELSEIF` / `ELSE` / `ENDIF` | 条件判断 |
| `SIF` | 单行条件 |
| `FOR` / `NEXT` | for 循环 |
| `WHILE` / `WEND` | while 循环 |
| `REPEAT` / `REND` | repeat 循环 |
| `SELECTCASE` / `CASE` / `CASEELSE` / `ENDSELECT` | 多分支选择 |
| `GOTO` | 跳转 |
| `CALL` / `JUMP` / `RETURN` | 函数调用与返回 |
| `CALLF` / `CALLFORMF` | 式中函数调用 |
| `CALLTRAIN` / `STOPCALLTRAIN` | 调教自动执行 |
| `TRY` / `CATCH` / `ENDCATCH` / `THROW` | 异常处理 |
| `BEGIN` | 切换到游戏阶段 |

### 输入

| 命令 | 说明 |
|------|------|
| `INPUT` | 数值输入 |
| `INPUTS` | 字符串输入 |
| `ONEINPUT` | 单键输入 |
| `TINPUT` | 限时数值输入 |
| `TONEINPUT` | 限时单键输入 |
| `FLOWINPUT` | 流程输入 |
| `GETKEY` | 获取按键 |
| `INPUTMOUSEKEY` | 鼠标按键输入 |

### 角色操作

| 命令 | 说明 |
|------|------|
| `ADDCHARA` / `ADDCOPYCHARA` / `ADDDEFCHARA` / `ADDVOIDCHARA` | 添加角色 |
| `DELCHARA` / `DELALLCHARA` | 删除角色 |
| `COPYCHARA` / `SWAPCHARA` | 复制/交换角色 |
| `FINDCHARA` / `PICKUPCHARA` / `GETCHARA` | 查找角色 |
| `SORTCHARA` | 排序角色 |
| `CHKCHARADATA` / `FIND_CHARADATA` | 检查/查找角色数据 |

### 系统

| 命令 | 说明 |
|------|------|
| `BEGIN` | 切换游戏阶段（FIRST/SHOP/TRAIN/TITLE 等）|
| `QUIT` / `RESTART` / `FORCE_QUIT` | 退出/重启 |
| `SAVEGAME` / `LOADGAME` | 存档/读档 |
| `SAVEDATA` / `LOADDATA` / `RESETDATA` | 数据存取 |
| `SAVEGLOBAL` / `LOADGLOBAL` / `RESETGLOBAL` | 全局数据 |
| `SAVECHARA` / `LOADCHARA` | 角色数据 |
| `SAVETEXT` / `LOADTEXT` | 文本存取 |
| `CHKDATA` / `DELDATA` | 检查/删除存档 |

### 图形

| 命令 | 说明 |
|------|------|
| `GCREATE` / `GCREATED` / `GCREATEFROMFILE` / `GDISPOSE` | 画布创建/销毁 |
| `GDRAWG` / `GDRAWGWITHMASK` / `GDRAWGWITHROTATE` | 画布绘制 |
| `GDRAWTEXT` / `GDRAWSPRITE` / `GDRAWLINE` | 文本/精灵/线条绘制 |
| `GFILLRECTANGLE` | 矩形填充 |
| `GSETCOLOR` / `GSETBRUSH` / `GSETPEN` / `GSETFONT` | 图形属性设置 |
| `GGETCOLOR` / `GGETBRUSH` / `GGETFONT` / `GGETPEN` | 图形属性获取 |
| `GSAVELOAD` | 画布保存/加载 |
| `GCLEAR` | 清空画布 |
| `SPRITECREATE` / `SPRITEDISPOSE` / `SPRITEMOVE` / `SPRITESETPOS` | 精灵操作 |
| `SPRITEANIMECREATE` / `SPRITEANIMEADDFRAME` | 精灵动画 |
| `CBGSETG` / `CBGSETSPRITE` / `CBGSETBMAPG` / `CBGCLEAR` | 按钮背景图形 |

### 音频

| 命令 | 说明 |
|------|------|
| `PLAYSOUND` / `STOPSOUND` | 音效播放/停止 |
| `PLAYBGM` / `STOPBGM` | BGM 播放/停止 |
| `SETSOUNDVOLUME` / `SETBGMVOLUME` | 音量设置 |
| `EXISTSOUND` | 音效文件存在检查 |

### DataTable（数据库）

| 命令 | 说明 |
|------|------|
| `DT_CREATE` / `DT_EXIST` / `DT_RELEASE` / `DT_CLEAR` | 创建/检查/释放/清空 |
| `DT_COLUMN_ADD` / `DT_COLUMN_EXIST` / `DT_COLUMN_REMOVE` / `DT_COLUMN_LENGTH` | 列操作 |
| `DT_ROW_ADD` / `DT_ROW_SET` / `DT_ROW_REMOVE` / `DT_ROW_LENGTH` | 行操作 |
| `DT_CELL_GET` / `DT_CELL_GETS` / `DT_CELL_SET` / `DT_CELL_ISNULL` | 单元格操作 |
| `DT_SELECT` | 条件查询 |
| `DT_TOXML` / `DT_FROMXML` | XML 序列化 |

### MAP（关联数组）

| 命令 | 说明 |
|------|------|
| `MAP_CREATE` / `MAP_EXIST` / `MAP_RELEASE` | 创建/检查/释放 |
| `MAP_GET` / `MAP_HAS` / `MAP_SET` / `MAP_REMOVE` | 键值操作 |
| `MAP_SIZE` / `MAP_CLEAR` | 大小/清空 |
| `MAP_GETKEYS` | 获取所有键 |
| `MAP_TOXML` / `MAP_FROMXML` | XML 序列化 |

### XML

| 命令 | 说明 |
|------|------|
| `XML_DOCUMENT` / `XML_RELEASE` / `XML_EXIST` | 创建/释放/检查 |
| `XML_GET` / `XML_SET` | 获取/设置值 |
| `XML_ADDNODE` / `XML_REMOVENODE` / `XML_REPLACE` | 节点操作 |
| `XML_ADDATTRIBUTE` / `XML_REMOVEATTRIBUTE` | 属性操作 |
| `XML_TOSTR` | 转字符串 |

### 式中函数（常用）

| 函数 | 签名 | 说明 |
|------|------|------|
| `ABS` | `int ABS(int)` | 绝对值 |
| `MAX` / `MIN` | `int MAX(int, int)` | 最大/最小值 |
| `RAND` | `int RAND(int, int)` | 随机数 |
| `STRLEN` / `STRLENS` | `int STRLENS(str)` | 字符串长度 |
| `SUBSTRING` / `SUBSTRINGU` | `str SUBSTRING(str, int, int)` | 子字符串 |
| `STRFIND` / `STRFINDU` | `int STRFIND(str, str)` | 字符串查找 |
| `SPLIT` | `int SPLIT(str, str, ref)` | 字符串分割 |
| `TOSTR` | `str TOSTR(int)` | 数值转字符串 |
| `TOINT` | `int TOINT(str)` | 字符串转数值 |
| `EXISTFILE` | `int EXISTFILE(str)` | 文件存在检查 |
| `ENUMFILES` | `int ENUMFILES(str)` | 文件枚举 |
| `GETCONFIG` / `GETCONFIGS` | `int/str GETCONFIG(str)` | 获取配置值 |
| `HTML_TOPLAINTEXT` | `str HTML_TOPLAINTEXT(str)` | HTML 转纯文本 |

## 回答约束

- **优先简洁回答**：先在本文档速查表中找到答案，需要精确签名时再打开具体 `references/` 文件。
- **默认值以文档为准**：API 签名、参数、返回值以 `references/` 内文本为准，不凭经验补全。
- **ERB 风格优先**：示例代码使用 `PRINT` 系、`;` 注释、`@` 函数等 ERA Basic 风格，不要带入其他语言的语法。
- **⚠️ ERA 没有类、模块、文件作用域等概念**：所有函数都是平级的全局函数，不同文件内也不得存在同名函数。同时只存在两种变量，即 ERH 中的全局变量、`@函数名` 的下一行一行一个的函数内局部变量。**禁止在函数外部写变量**，它不会像高级语言一样变成文件类的局部变量，而是在加载时变成一个孤立的行引发报错。（详见`references\csv-reference\erb-format.md`）
- **默认写 Emuera 代码**：直接使用 Emuera 的全部特性（`;!;` 注释、FORM 语法、式中函数、DataTable/MAP/XML 等）。只有在用户明确要求兼容旧版 eramaker 时才查阅 `references/eramaker-diff/differences.md`。
- **CSV 列名以项目为准**：CSV 文件（如 talent.csv、abl.csv）中的列名由各游戏项目自行定义，本文档中出现的列名仅为示例。编写代码时应查阅目标项目的实际 CSV 文件。
- **使用统一术语**：回答中使用「数组」「变量」「赋值」「字符串」「参数」等中文术语，与本文档保持一致。

## 常用查找命令

从 skill 目录执行：

```bash
# 按关键词命中文件
rg -n "PRINT\|IF\|CALL" references/INDEX.md

# 查找特定命令
rg -n "^\[.*PRINT" references/INDEX.md
```

从仓库根目录执行时，路径前加 `emuera-basic/`。
