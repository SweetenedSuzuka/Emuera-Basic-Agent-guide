# 输入命令

## INPUT / INPUTS — 数值/字符串输入

```
INPUT [默认值, 允许点击, 允许跳过]
INPUTS [默认值, 允许点击, 允许跳过]
```

等待玩家输入。`INPUT` 将数值存入 `RESULT`，`INPUTS` 将字符串存入 `RESULTS`。

### 参数

| 参数 | 类型 | 说明 |
|------|------|------|
| 第1参数 | int/string | 默认值（空输入时使用）。省略时 `INPUT` 要求重新输入，`INPUTS` 接受空字符串 |
| 第2参数 | int | 非 0 时，鼠标点击等同于按回车：`INPUTS` 时 `RESULTS` 变为空字符串、`RESULTS:1` 存按钮索引；左键 → `RESULT:1=1`，右键 → `RESULT:1=2`。同时按 Shift/Ctrl/Alt 则 `RESULT:2` 保存按键状态（bit 16/17/18）。省略时不启用此功能 |
| 第3参数 | int | 非 0 时，右键跳过期间不等待输入，但仍应用默认值。与第 2 参数联用时默认值分别流入 `RESULT:1`/`RESULTS:1`，不联用则流入 `RESULT:0`/`RESULTS:0`。省略时不启用此功能 |

```
; 基本用法
INPUT 请输入数字：
PRINTFORML 你输入了：{RESULT}

; 带默认值和鼠标点击
INPUT 50, 1
; 直接回车 → RESULT = 50
; 鼠标左键 → RESULT = 50, RESULT:1 = 1
```

## ONEINPUT / ONEINPUTS — 单字符输入

```
ONEINPUT [默认值]
ONEINPUTS [默认值]
```

输入单个字符后自动继续（无需按回车）。

- 粘贴多字符时只取第一个字符。
- 默认值为负数（`ONEINPUT`）或空字符串（`ONEINPUTS`）时，参数无效，等同于无参数。
- 启用键盘宏时可能无法正常工作（引擎限制）。

```
ONEINPUT 1
; 直接回车 → RESULT = 1
; 按 5 → RESULT = 5
```

## INPUTANY — 不限类型的任意输入

```
INPUTANY
```

等待玩家输入，**同时接受数值和字符串**。不限制格式——玩家可以输入任意内容。

- 无参数。
- 玩家输入数值时，结果存入 `RESULT`
- 玩家输入字符串时（包括点击 `PRINTBUTTON`），结果存入 `RESULTS`
- 执行时可点击 `PRINTBUTTON` 和 `[数字]` 按钮
- **不是式中函数**，只能以命令形式使用

```
; 基本用法
PRINTL [0] 选项0
PRINTL [1] 选项1
PRINTBUTTON "[A] 选项A", "A"
PRINTL
INPUTANY

; 数值输入 → RESULT 获得数字
; 字符串输入/按钮点击 → RESULTS 获得字符串
; 通过三目运算符判断输入类型：
PRINTFORMW \@ RESULTS != "" ? %RESULTS% # {RESULT} \@ 被输入了
```

## TINPUT / TINPUTS — 限时输入

```
TINPUT 超时毫秒, 默认值[, 显示剩余时间, 超时文字, 允许点击]
TINPUTS 超时毫秒, 默认值[, 显示剩余时间, 超时文字, 允许点击]
```

| 参数 | 类型 | 说明 |
|------|------|------|
| 第1参数 | int | 超时时间（毫秒），100ms 以下的精度无法保证 |
| 第2参数 | int/string | 超时时的默认返回值 |
| 第3参数 | int | 是否显示剩余时间（0=隐藏，非0=显示）。省略时默认显示 |
| 第4参数 | string | 超时时显示的文字。空字符串 = 清除计时器后继续。若指定则第3参数不可省略 |
| 第5参数 | int | 非 0 时鼠标点击等同于回车，行为同 INPUT 第 2 参数。省略时不启用此功能 |
| 第6参数 | int | 非 0 时，右键跳过期间不等待输入（同 INPUT 第 3 参数）。省略时不启用此功能 |

`TINPUTS` 与 `INPUTS` 同样支持宏表达式。超时后 `ISTIMEOUT` 变为 1，`RESULT` / `RESULTS` 设为默认值。

```
TINPUT 5000, -1
SIF ISTIMEOUT
    PRINTL 超时了！

; 带超时提示
TINPUT 3000, 0, 1, "时间到！"
```

## TONEINPUT / TONEINPUTS — 限时单字符输入

```
TONEINPUT 超时毫秒, 默认值[, 显示剩余时间, 超时文字, 允许点击]
TONEINPUTS 超时毫秒, 默认值[, 显示剩余时间, 超时文字, 允许点击]
```

`ONEINPUT` 与 `TINPUT` 的结合：输入单个字符后自动继续，同时带有超时限制。参数含义与 `TINPUT` 完全相同。

## FLOWINPUT / FLOWINPUTS — 流程输入控制

```
FLOWINPUT 默认值[, 允许左键, 允许跳过, 强制跳过]
FLOWINPUTS 开关[, 默认值]
```

仅在 `BEGIN` 切换到的系统流程中生效（如 `@SHOW_SHOP` 内的 `INPUT`）。

**`FLOWINPUT`**：对流程内的 `INPUT` 命令统一设置：
- 第 1 参数：默认值
- 第 2 参数：非 0 允许左键点击（同 INPUT 第 2 参数扩展）
- 第 3 参数：非 0 允许跳过（同 INPUT 第 3 参数扩展）
- 第 4 参数：非 0 时强制跳过（直接填入默认值）。省略时不启用此功能

**`FLOWINPUTS`**：第 1 参数非 0 时，流程内所有 `INPUT` 变为 `INPUTS` 行为。

```
; SHOP 中自动填入默认值，允许鼠标点击
FLOWINPUT 0, 1
```

## INPUTMOUSEKEY — 鼠标/键盘原始输入

```
INPUTMOUSEKEY [超时毫秒]
```

直接捕获鼠标和键盘事件，可检测 `ONEINPUT` 无法捕获的功能键、方向键等。

### 返回值

| `RESULT:0` | 含义 | 其他返回值 |
|------------|------|-----------|
| 1 | 鼠标按下 | `RESULT:1` = 按键值（左 0x100000 / 右 0x200000 / 中 0x400000）；`RESULT:2` = X 坐标；`RESULT:3` = Y 坐标（**始终为负值**，以客户区左下角为原点）；`RESULT:4` = CBG 按钮映射颜色（0xRRGGBB）或 -1；`RESULT:5` = 点击的按钮值 |
| 2 | 鼠标滚轮 | `RESULT:1` = 滚轮量（大值，如 120）；`RESULT:2` = X 坐标；`RESULT:3` = Y 坐标 |
| 3 | 键盘按下 | `RESULT:1` = 键码（不含修饰键）；`RESULT:2` = 键码（含 Alt/Ctrl/Shift 修饰） |
| 4 | 超时 | — |

- 输入等待期间 ESC 跳过和宏功能均不可用。
- 此命令**不执行任何 PRINT 输出**，需要自行处理超时显示等。
- 省略超时参数或 ≤0 时，不启用超时处理。

## GETKEY / GETKEYTRIGGERED — 按键状态检测

```
int GETKEY(键码)
int GETKEYTRIGGERED(键码)
```

检查按键状态（不阻塞）。

- `GETKEY`：按键被按下时返回 1，否则返回 0。
- `GETKEYTRIGGERED`：按键**刚被按下瞬间**返回 1，持续按住时返回 0。

仅在 Emuera 窗口处于激活状态时有效。键码参考 Windows `GetKeyState()` API（可在 ERB 中引用 `_VirtualKey.ERH` 中定义的 `VK_*` 常量）。

```
; 检查回车键是否按下
IF GETKEY(VK_RETURN)
    ; ...
ENDIF
```

## MOUSEB / MOUSEX / MOUSEY — 鼠标状态

```
int MOUSEB    ; 鼠标按键状态：0=未按下 / 非0=按下
int MOUSEX    ; 鼠标 X 坐标
int MOUSEY    ; 鼠标 Y 坐标
```

## GETNUM — 从字符串提取数值

```
GETNUM 源字符串, 目标变量名
```

从字符串中解析数值并写入指定变量。

```
GETNUM "价格: 500 G", MONEY
; MONEY = 500
```

## BINPUT / BINPUTS — 受限按钮输入

```
BINPUT [默认值, 允许点击, 允许跳过]
BINPUTS [默认值, 允许点击, 允许跳过]
```

只接受**已按钮化**（被 `PRINTBUTTON` 等渲染为按钮）的值的输入。

- 限制的不是输入方式，而是可接受的值。键盘和鼠标操作均可正常使用，但只接受当前画面中存在的按钮值。
- 若当前没有任何按钮，直接应用默认值。若无默认值则报错。
- 参数含义同 `INPUT` 的第 2、第 3 参数。

```
; 假设画面中有按钮 [0]～[9]
BINPUT
; 玩家输入的值必须是 0~9 之一，输入 99 会被拒绝
```
