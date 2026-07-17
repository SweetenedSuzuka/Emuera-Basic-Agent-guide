# 变量体系

ERA Basic 的变量系统是理解整个语言的关键。本文档按类别详细说明所有可用变量。

## 变量概览

| 类别 | 示例 | 类型 | 数组 | 赋值 | 保存 | 说明 |
|------|------|:--|:--|:--|:--|------|
| 单字母变量 | `A`～`Z` | 整数 | 一维 | ○ | ○ | 基础通用变量 |
| 游戏状态 | `MONEY`、`DAY`、`TIME` | 整数 | 一维 | ○ | ○ | 游戏核心状态 |
| 角色引用 | `TARGET`、`MASTER`、`ASSI`、`PLAYER` | 整数 | 一维 | ○ | ○ | 当前操作对象 |
| 命令 | `SELECTCOM`、`PREVCOM`、`NEXTCOM` | 整数 | 一维 | ○ | ○ | 调教命令相关 |
| 全局标志 | `FLAG` | 整数 | 一维 | ○ | ○ | 全局整数标志 |
| 回合标志 | `TFLAG` | 整数 | 一维 | ○ | ○ | 每回合初始化 0 |
| 角色标志 | `CFLAG` | 整数 | 角色+一维 | ○ | ○ | 角色整数标志 |
| 字符串 | `STR`、`SAVESTR` | 字符串 | 一维 | ○ | STR×/SAVESTR○ | 文本存储 |
| 角色属性 | `BASE`、`ABL`、`TALENT`、`EXP`、`MARK` | 整数 | 角色+一维 | ○ | ○ | CSV 定义名引用 |
| 角色参数 | `PALAM`、`STAIN`、`EX`、`SOURCE` | 整数 | 角色+一维 | ○ | ○ | 每回合参数 |
| 局部变量 | `LOCAL`、`LOCALS`、`ARG`、`ARGS` | 整数/字符串 | 一维 | ○ | × | 函数内局部 |
| 全局共享 | `GLOBAL`、`GLOBALS` | 整数/字符串 | 一维 | ○ | ※ | SAVEGLOBAL/LOADGLOBAL |
| CSV 引用 | `ITEMNAME`、`TALENTNAME` 等 | 字符串 | 一维 | × | × | CSV 定义值只读 |
| 用户定义 | `#DIM`/`#DIMS` 定义 | 任意 | 1～3维 | 可设定 | 可设定 | 自定义 |

## 基础变量详解

### 单字母变量（A～Z）

基础游戏变量，全部为整数类型一维数组。

```
A = 100
A:0 = 50
A:1 = A:0 + 30
```

### 游戏状态变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `MONEY` | 所持金钱 | — |
| `DAY` | 当前日数 | — |
| `TIME` | 当前时间 | — |
| `ITEM` | 道具数量（可用 `ITEMNAME` 字符串索引） | — |
| `ITEMSALES` | 道具销售额 | — |
| `BOUGHT` | 已购买标志 | — |
| `CHARANUM` | 角色登记数（只读） | — |
| `RAND` | 随机数（只读） | — |
| `RANDDATA` | 随机数生成器内部状态（可读写） | — |
| `LINECOUNT` | 当前输出行数（只读） | — |
| `ISTIMEOUT` | TINPUT 系超时标志（只读） | 0 |

| `ISTIMEOUT` | TINPUT 系超时标志（只读） | 0 |

## RANDDATA

随机数生成器内部状态。**仅调试用**，不应在游戏逻辑中使用。

- 类型：int
- 可读可写
- 写入 `RANDDATA` 可以手动设置随机数种子（与 `RANDOMIZE` 不同，它直接设置内部状态）

### 角色引用变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `TARGET` | 当前指令目标角色 | `TARGET:0 = 1` |
| `MASTER` | 主人角色编号 | — |
| `ASSI` | 助手角色编号 | `ASSI:0 = -1` |
| `PLAYER` | 玩家（操作者）角色编号 | — |
| `NO` | 角色编号（`CHARA**.CSV` 的 `番号` 列，即登记编号） | — |
| `ISASSI` | 助手标志（`CHARA**.CSV` 的 `助手` 列） | — |
| `ASSIPLAY` | 调教中助手是否参与（PLAY 状态）。BEGIN TRAIN 时初始化为 `ASSIPLAY:0 = 0`。 |

### 命令相关变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `SELECTCOM` | 当前选择的调教命令编号 | — |
| `PREVCOM` | 前次命令编号 | `PREVCOM:0 = -1`（BEGIN TRAIN 时） |
| `NEXTCOM` | 下次命令编号（不推荐） | `NEXTCOM:0 = -1`（BEGIN TRAIN 时） |

### 标志类变量

| 变量 | 说明 | 初始化时机 |
|------|------|------------|
| `FLAG` | 全局标志（保存） | — |
| `TFLAG` | 回合标志 | BEGIN TRAIN 时全 0 |
| `CFLAG` | 角色标志（保存） | — |
| `CDFLAG` | 角色三维标志（保存） | — |

### 字符串变量

| 变量 | 类型 | 可保存 | 说明 |
|------|:--|:--|------|
| `STR` | 字符串 | × | 由 `STR.CSV` 定义初始值 |
| `SAVESTR` | 字符串 | ○ | 可保存字符串 |
| `TSTR` | 字符串 | × | 回合字符串（BEGIN TRAIN 时清空） |
| `CSTR` | 角色+字符串 | ○ | 角色字符串 |
| `NAME` | 角色+字符串 | ○ | 角色名称 |
| `CALLNAME` | 角色+字符串 | ○ | 角色称呼 |
| `RESULTS` | 字符串 | × | 命令执行结果的字符串 |
| `NICKNAME` | 角色+字符串 | ○ | 角色昵称 |
| `MASTERNAME` | 角色+字符串 | ○ | 主人称呼 |

### 角色属性变量（角色+一维整数类型）

全部可保存。

| 变量 | 参考 CSV | 说明 |
|------|----------|------|
| `BASE` | `BASENAME`（base.csv） | 基础值 |
| `MAXBASE` | `BASENAME`（base.csv） | 基础最大值 |
| `ABL` | `ABLNAME`（abl.csv） | 能力 |
| `TALENT` | `TALENTNAME`（talent.csv） | 素质 |
| `EXP` | `EXPNAME`（exp.csv） | 经验 |
| `MARK` | `MARKNAME`（mark.csv） | 刻印 |
| `RELATION` | `NAME` 或 `CALLNAME` | 相性 |
| `JUEL` | `PALAMNAME`（palam.csv） | 珠 |
| `CFLAG` | `CFLAGNAME`（cflag.csv） | 角色标志 |
| `EQUIP` | `EQUIPNAME`（equip.csv） | 装备 |
| `TEQUIP` | `TEQUIPNAME`（tequip.csv） | 回合装备 |
| `PALAMLV` | —（_replace.csv 的 `PALAMLVの初期値`） | 参数等级（非角色变量） |
| `EXPLV` | —（_replace.csv 的 `EXPLVの初期値`） | 绝顶等级（非角色变量） |
| `EJAC` | — | 射精相关（`:0 = 10000`） |
| `PBAND` | —（_replace.csv 的 `PBANDの初期値`） | 苦痛耐性（非角色变量） |

### 回合参数（角色+一维整数类型）

| 变量 | 参考 CSV | 说明 | 初始化时机 |
|------|----------|------|------------|
| `PALAM` | `PALAMNAME` | 参数 | BEGIN TRAIN 时全 0 |
| `GOTJUEL` | `PALAMNAME` | 获得珠 | BEGIN TRAIN 时全 0 |
| `STAIN` | `STAINNAME` | 污渍 | BEGIN TRAIN 时初始化 |
| `EX` | `EXNAME` | 绝顶经验 | BEGIN TRAIN 时全 0 |
| `SOURCE` | `SOURCENAME` | 调教来源 | BEGIN TRAIN / @SOURCE_CHECK 时全 0 |
| `NOWEX` | `EXNAME` | 本次绝顶经验 | @EVENTCOM 之前全 0 |
| `UP` | `PALAMNAME` | 上升量 | @SHOW_USERCOM 结束时全 0 |
| `DOWN` | `PALAMNAME` | 下降量 | @SHOW_USERCOM 结束时全 0 |
| `LOSEBASE` | `BASENAME` | 基础减少 | @SHOW_USERCOM 结束时全 0 |
| `DOWNBASE` | `BASENAME` | 基础下降 | @SHOW_USERCOM 结束时全 0 |
| `CUP` | `PALAMNAME` | 角色上升量 | @SHOW_USERCOM 结束时全 0 |
| `CDOWN` | `PALAMNAME` | 角色下降量 | @SHOW_USERCOM 结束时全 0 |
| `TCVAR` | `TCVARNAME` | 回合角色变量 | BEGIN TRAIN 时全 0 |

### 局部变量

| 变量 | 类型 | 说明 |
|------|:--|------|
| `LOCAL` | 整数 | 函数内局部（推荐改用 `#DIM`） |
| `LOCALS` | 字符串 | 函数内局部字符串（推荐改用 `#DIMS`） |
| `ARG` | 整数 | 函数参数数值 |
| `ARGS` | 字符串 | 函数参数字符串 |
| `RESULT` | 整数 | 命令执行结果 |
| `RESULTS` | 字符串 | 命令执行结果字符串 |

`LOCAL` 和 `LOCALS` 的元素个数可通过 `#LOCALSIZE` 和 `#LOCALSSIZE` 在函数内设定：

```
@FUNC_NAME
#LOCALSIZE 500
#LOCALSSIZE 200
    LOCAL:10 = 123
    LOCALS:5 = 字符串
```

### 全局变量

| 变量 | 类型 | 说明 |
|------|:--|------|
| `GLOBAL` | 整数 | 跨存档共享（SAVEGLOBAL/LOADGLOBAL） |
| `GLOBALS` | 字符串 | 跨存档共享字符串 |

### CSV 引用变量（只读）

全部不可赋值、不可保存。

| 变量 | 对应文件 |
|------|----------|
| `ITEMNAME` | item.csv |
| `ITEMPRICE` | item.csv |
| `ABLNAME` | abl.csv |
| `TALENTNAME` | talent.csv |
| `EXPNAME` | exp.csv |
| `MARKNAME` | mark.csv |
| `PALAMNAME` | palam.csv |
| `TRAINNAME` | train.csv |
| `BASENAME` | base.csv |
| `EQUIPNAME` | equip.csv / tequip.csv |
| `STAINNAME` | stain.csv |
| `EXNAME` | ex.csv |
| `SOURCENAME` | source.csv |
| `FLAGNAME` | flag.csv |
| `TFLAGNAME` | tflag.csv |
| `CFLAGNAME` | cflag.csv |
| `STRNAME` | strname.csv（※注意：是 strname.csv 而不是 str.csv） |
| `TSTRNAME` | tstr.csv |
| `CSTRNAME` | cstr.csv |
| `SAVESTRNAME` | savestr.csv |
| `TCVARNAME` | tcvar.csv |
| `CDFLAGNAME1` / `CDFLAGNAME2` | cdflag1.csv / cdflag2.csv |
| `GLOBALNAME` | global.csv |
| `GLOBALSNAME` | globals.csv |

### CSV 文件名到引用变量的完整对照表

| CSV 文件名 | 引用变量 | 说明 |
|-----------|---------|------|
| item.csv | ITEMNAME, ITEMPRICE | 道具名称、价格 |
| train.csv | TRAINNAME | 调教命令名称 |
| abl.csv | ABLNAME | 能力名称 |
| base.csv | BASENAME | 基础值名称 |
| talent.csv | TALENTNAME | 素质名称 |
| exp.csv | EXPNAME | 经验名称 |
| mark.csv | MARKNAME | 刻印名称 |
| palam.csv | PALAMNAME | 参数名称 |
| ex.csv | EXNAME | 绝顶经验名称 |
| source.csv | SOURCENAME | 调教来源名称 |
| stain.csv | STAINNAME | 污渍名称 |
| strname.csv | STRNAME | 字符串变量名称 |
| equip.csv | EQUIPNAME | 装备名称 |
| tequip.csv | TEQUIPNAME | 回合装备名称 |
| cflag.csv | CFLAGNAME | 角色标志名称 |
| tcvar.csv | TCVARNAME | 回合角色变量名称 |
| cdflag1.csv / cdflag2.csv | CDFLAGNAME1 / CDFLAGNAME2 | 三维角色标志名称 |
| global.csv | GLOBALNAME | 全局变量名称 |
| globals.csv | GLOBALSNAME | 全局字符串名称 |

说明：这些都是**只读**变量，通过对应的 CSV 文件自动定义，不可赋值。

> **注意**：`ITEMSALES` 不是 CSV 引用只读变量，而是可赋值、可保存的普通变量。`str.csv` 是 `STR` 变量的初始值文件，不是 `STRNAME` 的定义文件（`STRNAME` 对应 `strname.csv`）。

### gamebase.csv 变量（只读）

| 变量 | 对应 CSV 列 | 类型 | 说明 |
|------|------------|:---:|------|
| `GAMEBASE_AUTHOR` | 作者 | str | 制作者名 |
| `GAMEBASE_INFO` | 追加情報 | str | 追加信息 |
| `GAMEBASE_YEAR` | 製作年 | str | 制作年份 |
| `GAMEBASE_TITLE` | タイトル | str | 游戏标题 |
| `GAMEBASE_GAMECODE` | コード | int | 游戏代码（用于存档识别） |
| `GAMEBASE_VERSION` | バージョン | int | 游戏版本号 |
| `GAMEBASE_ALLOWVERSION` | バージョン違い認める | int | 允许跨版本读档（1=允许） |
| `GAMEBASE_DEFAULTCHARA` | 最初からいるキャラ | int | 初始角色列表 |
| `GAMEBASE_NOITEM` | アイテムなし | int | "无道具"时的显示编号 |

说明：这些是从 gamebase.csv 自动读取的只读变量。

### 其他特殊变量

| 变量 | 类型 | 说明 |
|------|:--|------|
| `WINDOW_TITLE` | 字符串（无维） | 窗口标题，可赋值 |
| `MONEYLABEL` | 字符串（无维，只读） | 货币单位，默认 `$` |
| `DRAWLINESTR` | 字符串（无维，只读） | DRAWLINE 字符 |
| `LASTLOAD_VERSION` | 整数（无维，只读） | 上次加载的版本 |
| `LASTLOAD_NO` | 整数（无维，只读） | 上次加载的存档号 |
| `LASTLOAD_TEXT` | 字符串（无维，只读） | 上次加载的文本 |
| `SAVEDATA_TEXT` | 字符串（无维） | 存档描述文本 |
| `__INT_MAX__` | 整数（无维，只读） | 9223372036854775807 |
| `__INT_MIN__` | 整数（无维，只读） | -9223372036854775808 |
| `NOITEM` | 整数（一维） | gamebase.csv「アイテムなし」 |

### 多维度变量

> ⚠️ **已废弃（obsolete）**：`DITEMTYPE`、`DA`～`DE`、`TA`、`TB` 均为遗留的多维数组变量。推荐使用 `#DIM`/`#DIMS` 自定义变量代替。

| 变量 | 维度 | 说明 |
|------|:---:|------|
| `DITEMTYPE` | 2维 | 道具类型（DITEMTYPE:道具编号:类型编号） |
| `DA` | 2维 | 二次元整数数组 |
| `DB` | 2维 | 二次元整数数组 |
| `DC` | 2维 | 二次元整数数组 |
| `DD` | 2维 | 二次元整数数组 |
| `DE` | 2维 | 二次元整数数组 |
| `TA` | 3维 | 三次元整数数组 |
| `TB` | 3维 | 三次元整数数组 |

维度大小由 VariableSize.csv 定义，ERB 中不可更改。

## 用户定义变量

### `#DIM`（整数类型）/ `#DIMS`（字符串类型）

```
; 一维数组
#DIM MY_INT, 100
#DIMS MY_STR, 50

; 二维数组
#DIM MY_2D, 10, 20

; 三维数组
#DIM MY_3D, 5, 10, 15

; 带初始值
#DIM CONST HOGE = 1,2,3
#DIMS CONST SHOGE = "A", "B", "C"

; 动态变量
#DIM DYNAMIC DYN_VAR, 100

; 引用类型变量
#DIM REF REF_VAR, 0

; ERH 中的扩展
#DIM SAVEDATA MY_SAVED, 100       ; 可保存
#DIM CHARADATA C_VAR, 50          ; 角色变量
#DIM GLOBAL G_VAR, 100            ; 全局变量
#DIM GLOBAL SAVEDATA GS_INT, 100  ; 可保存全局
#DIM CHARADATA SAVEDATA CS_VAR, 50 ; 可保存角色变量
```

## 注意事项

- `CHARANUM` 和 `RAND` 为只读，禁止赋值
- `NAME` 和 `CALLNAME` 可赋值
- `COUNT:0` 是 REPEAT 循环的计数器
- `VariableSize.csv` 可以改变现有变量的元素个数，设定 `-1` 可禁止使用
- `#DIMS SAVEDATA` 定义可保存多维变量时，需要启用「以二进制格式保存存档数据」
- 禁止变量在 ERB 中赋值或引用会报错，系统需要时返回 `-1`
