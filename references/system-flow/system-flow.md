# 系统流程

本文档说明 Emuera 的游戏系统流程。

## 流程概览

```
启动 → CSV/ERB 读取 → TITLE → FIRST → SHOP ⇄ TRAIN
```

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

自动执行结束后系统自动调用 `@CALLTRAINEND`。

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
| `@SOURCE_CHECK` | 事件 | SOURCE 检查时 |
| `@SAVEINFO` | 事件 | 存档信息获取时 |
| `@CALLTRAINEND` | 普通 | CALLTRAIN 结束后 |
| `@USERCOM` | 事件 | 用户自定义命令输入 |

## BEGIN 命令

切换游戏阶段：

```
BEGIN TITLE    ; 回到标题
BEGIN FIRST    ; 新游戏
BEGIN SHOP     ; 商店
BEGIN TRAIN    ; 调教
```

`BEGIN` 会中断当前执行流程跳转到对应阶段。

## 重要注意事项

1. `@EVENTFIRST` 中必须执行 `BEGIN`，否则错误结束
2. `@SYSTEM_TITLE` 中也必须执行 `BEGIN` 或 `LOADDATA`
3. `NEXTCOM` 有已知重大缺陷，不推荐新规使用
4. TRAIN 中退出的变量初始化不要依赖于 BEGIN TRAIN（因为 SHOP 中存档时会保留 TRAIN 中状态）
5. 建议在 `@SAVEINFO` 中将角色的 `GOTJUEL`、`TEQUIP`、`EX`、`PALAM` 等赋 0 以节省存档大小
