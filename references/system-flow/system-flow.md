# 系统流程

本文档说明 Emuera 的游戏系统流程。

## 流程概览

```
启动 → CSV/ERB 读取 → TITLE → FIRST → SHOP ⇄ TRAIN
                                              ↕
                                        AFTERTRAIN / ABLUP / TURNEND
```

### AFTERTRAIN 阶段

调教后阶段。`BEGIN AFTERTRAIN` 执行后进入，系统自动调用 `@EVENTEND`（事件函数，可多重定义）。

### TURNEND 阶段

回合结束阶段。`BEGIN TURNEND` 执行后进入，系统自动调用 `@EVENTTURNEND`（事件函数，可多重定义）。

## TITLE 阶段

时机：启动后 ERB 读取完毕、`BEGIN TITLE` 执行后。

- `@SYSTEM_TITLE` 定义时：调用 `@SYSTEM_TITLE`，其他什么都不做
- `@SYSTEM_TITLE` 中未执行 `BEGIN` 或 `LOADDATA` 则 RETURN 后报错结束
- `@SYSTEM_TITLE` 未定义时：使用标准标题画面

标准标题画面选项：

| 选项 | 行为 |
|------|------|
| `[0] 从头开始` | 数据初始化 → `ADDCHARA 0` → `BEGIN FIRST` |
| `[1] 读档开始` | `@TITLE_LOADGAME`（如定义）→ 标准读档画面 |

### `@TITLE_LOADGAME`

标准标题画面选择「读档」时调用。

可自定义标题画面的读档界面。与 SAVEGAME 的读档画面略有不同。

## FIRST 阶段

时机：标题画面 `[0] 从头开始` 选择后、`BEGIN FIRST` 执行后。

- `@EVENTFIRST` 内必须执行 `BEGIN` 命令，否则报错结束
- `@EVENTFIRST` 是事件函数，可多重定义

## SHOP 阶段

时机：读档后、`BEGIN SHOP` 执行后。

- 读档后不执行 `@EVENTSHOP`
- `@SHOW_SHOP` 调用后要求输入

购物流程：
1. `@SHOW_SHOP` → 显示 `PRINT_ITEMSHOP`
2. 玩家输入 `0～99`（范围可由 `_replace.csv` 改变）
3. 数字输入 → 检查 `ITEMSALES` 和 `MONEY`
4. 判定失败 → 重新输入
5. 判定成功 → `BOUGHT` 设定 → `ITEM` 增加 → `MONEY` 减少 → `@EVENTBUY` → 返回 `@SHOW_SHOP`
6. 非数字输入 → `@USERSHOP`
7. 如无 `BEGIN` 命令，SHOP 永不退出

## TRAIN 阶段

时机：`BEGIN TRAIN` 执行后。

### 初始化（BEGIN TRAIN 时）

1. `ASSIPLAY:0` = 0
2. `PREVCOM:0` = -1
3. `NEXTCOM:0` = -1
4. 全部角色的 `GOTJUEL`、`TEQUIP`、`EX`、`PALAM`、`SOURCE` 清零
5. 全部角色的 `STAIN` 初始值设定
6. `TFLAG` 全要素 = 0

### 调教循环

1. `@SHOW_STATUS` 调用
2. 显示可执行的 TRAIN 列表（遍历 `@COM_ABLExx`，若未定义或返回值 ≠ 0 则可执行）
3. `@SHOW_USERCOM` 调用
4. `UP`、`DOWN`、`LOSEBASE`、`CUP`、`CDOWN` 初始化
5. 等待玩家输入
6. 输入检查 → `SELECTCOM` 设定
7. 全部角色的 `NOWEX` 全要素 = 0
8. `@EVENTCOM` 调用
9. 对应 `@COMxx` 调用
10. 检查 `SOURCE`、`UPCHECK` 等
11. 循环回步骤 1

### `CALLTRAIN` 自动执行

`CALLTRAIN` 可跳过玩家输入自动执行调教。

#### 执行机制

`CALLTRAIN` 启动后，引擎按以下循环自动执行：

1. **判定可用命令**：遍历所有 `@COM_ABLExx`（xx = 命令编号），检查哪些命令当前可用（未定义或返回值 ≠ 0 则为可执行）
2. **随机选择命令**：从可用命令中随机选一个
3. **执行命令**：调用对应的 `@COMxx`
4. **SOURCE 检查**：调用 `@SOURCE_CHECK`
5. **命令结束处理**：调用 `@EVENTCOMEND`
6. **回到步骤 1**：继续下一轮选择与执行

#### 中止自动执行

使用 `STOPCALLTRAIN` 命令中止自动执行。调用后引擎立即退出自动循环。

#### 自动执行结束

自动执行结束后系统自动调用 `@CALLTRAINEND`（非事件函数，不可多重定义）。

#### 调用方的执行暂停

**重要**：`CALLTRAIN` 开始后，调用方的代码在此过程中**暂停执行**，直到 `STOPCALLTRAIN` 被调用或所有可用命令执行完毕，调用方才继续执行 `CALLTRAIN` 之后的代码。

```
; 示例
CALLTRAIN
; ← 这里的代码在 CALLTRAIN 自动执行结束前不会执行
PRINTL 调教自动执行完毕
```

## ABLUP 流程（能力提升）

ABLUP 是 TRAIN 阶段中通过消耗"珠"（JUEL）来提升角色能力的子流程。

> 可通过 `BEGIN ABLUP` 直接从代码中进入此阶段。

### 流程图

```
@SHOW_JUEL              ; 决定可用的珠数（设置 JUEL 变量）
    ↓
@SHOW_ABLUP_SELECT      ; 显示能力提升选择（玩家选择要提升的能力）
    ↓
@ABLUP                  ; 实际提升能力值
    ↓
@USERABLUP              ; 用户自定义的额外 ABLUP 处理
```

### 详细说明

#### `@SHOW_JUEL`

通过 `JUEL` 变量设置可用的"珠"数。必须从 `JUEL:0` 开始连续赋值给 `JUEL` 数组的后续元素：

```
@SHOW_JUEL
    JUEL:0 = 能力A的珠数
    JUEL:1 = 能力B的珠数
    JUEL:2 = 能力C的珠数
    ; ... 以此类推
```

每个 `JUEL` 元素对应一项可提升的能力。

#### `@SHOW_ABLUP_SELECT`

显示能力提升选项列表，等待玩家输入（0～99）。

- 玩家输入的数字在 `JUEL` 数组元素数范围内时 → 触发对应能力的提升流程，进入 `@ABLUP`
- 玩家输入超出范围（或非数字）→ 进入 `@USERABLUP`（自定义处理）

#### `@ABLUP`

实际修改 `ABL`（能力值）的函数。引擎根据玩家在 `@SHOW_ABLUP_SELECT` 中的选择，调用 `@ABLUP` 对对应的能力值进行提升。

> **注意**：如果玩家选择的编号对应的 `@ABLUP` 未定义，引擎会**重新要求输入**（不会报错，也不会进入 `@USERABLUP`）。这与 `@SHOW_USERCOM` 中对应 `@COMxx` 未定义时也要求重新选择的行为类似。

#### `@USERABLUP`

用户自定义的额外 ABLUP 处理。当玩家在 `@SHOW_ABLUP_SELECT` 中输入了非标准选项（不在 `0～99` 范围内）时调用。进入 `@USERABLUP` 后引擎会返回到 `@SHOW_JUEL`。

### 完整循环流程

```
@SHOW_JUEL
    ↓
@SHOW_ABLUP_SELECT  ←────────────────┐
    ↓ (输入 0～99 且对应 @ABLUP 存在)  │
@ABLUP                                │
    ↓                                 │
回到 @SHOW_ABLUP_SELECT ──────────────┘
    ↓ (输入 0～99 但对应 @ABLUP 不存在)
重新要求输入 ─────────────────────────┘
    ↓ (输入超出 0～99)
@USERABLUP → 回到 @SHOW_JUEL ────────→ (循环)
```

## 主要系统函数一览

| 函数 | 类型 | 说明 |
|------|------|------|
| `@SYSTEM_TITLE` | 普通 | 自定义标题画面 |
| `@TITLE_LOADGAME` | 普通 | 自定义标题读档画面 |
| `@SYSTEM_AUTOSAVE` | 普通 | 自定义自动存档 |
| `@EVENTFIRST` | 事件 | 游戏开始时 |
| `@EVENTLOAD` | 事件 | 读档后 |
| `@EVENTSHOP` | 事件 | SHOP 开始时 |
| `@SHOW_SHOP` | 事件 | SHOP 画面表示 |
| `@USERSHOP` | 事件 | SHOP 用户自定义输入 |
| `@EVENTBUY` | 事件 | 购买后 |
| `@SHOW_STATUS` | 事件 | TRAIN 状态表示 |
| `@SHOW_USERCOM` | 事件 | TRAIN 命令选择前 |
| `@EVENTCOM` | 事件 | 命令执行前 |
| `@COM_ABLExx` | 事件 | 命令可否判定（xx=命令编号） |
| `@COMxx` | 事件 | 命令执行（xx=命令编号） |
| `@EVENTCOMEND` | 事件 | 命令执行后 |
| `@EVENTEND` | 事件 | AFTERTRAIN 阶段（调教结束后） |
| `@EVENTTURNEND` | 事件 | TURNEND 阶段（回合结束后） |
| `@SOURCE_CHECK` | 事件 | SOURCE 检查时 |
| `@SAVEINFO` | 事件 | 存档信息获取时 |
| `@CALLTRAINEND` | 普通 | CALLTRAIN 结束后 |
| `@USERCOM` | 事件 | 用户自定义命令输入 |

## BEGIN 命令

切换游戏阶段：

```
BEGIN TITLE       ; 回到标题
BEGIN FIRST       ; 新游戏
BEGIN SHOP        ; 商店
BEGIN TRAIN       ; 调教
BEGIN AFTERTRAIN  ; 调教后（@EVENTEND）
BEGIN ABLUP       ; 能力提升
BEGIN TURNEND     ; 回合结束（@EVENTTURNEND）
```

`BEGIN` 会中断当前执行流程跳转到对应阶段。

## 重要注意事项

1. `@EVENTFIRST` 中必须执行 `BEGIN`，否则错误结束
2. `@SYSTEM_TITLE` 中也必须执行 `BEGIN` 或 `LOADDATA`
3. `NEXTCOM` 有已知重大缺陷，不推荐在新项目中使用
4. TRAIN 中退出的变量初始化不要依赖于 BEGIN TRAIN（因为 SHOP 中存档时会保留 TRAIN 中状态）
5. 建议在 `@SAVEINFO` 中将角色的 `GOTJUEL`、`TEQUIP`、`EX`、`PALAM` 等赋 0 以节省存档大小
