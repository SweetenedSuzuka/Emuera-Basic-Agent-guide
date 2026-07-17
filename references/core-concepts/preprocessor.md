# 函数与预处理器

## 特殊系统函数

### `@EVENTLOAD`
加载存档后立即调用。可多重定义（事件函数）。
未定义时跳转至 `@SHOW_SHOP`。

### `@TITLE_LOADGAME`
标准标题画面选择「加载」时调用。
可自定义标题画面的读档界面。
**但若定义了 `@SYSTEM_TITLE`，则 `@TITLE_LOADGAME` 默认不再自动触发，需手动 `CALL`。**

### `@SYSTEM_AUTOSAVE`
自动存档时调用，可自定义自动存档内容。

### `@SYSTEM_TITLE`
CSV 加载完成后调用。`BEGIN TITLE` 也会触发。
用于自定义标题画面，未定义时使用标准标题画面。

### `@CALLTRAINEND`
`CALLTRAIN` 自动执行结束后系统自动调用。
**不是**事件函数，不可多重定义。

## 自定义函数的参数

### 书写格式

> 函数参数和调用的完整说明见 `references/core-concepts/user-defined-functions.md`。此处仅列出预处理器相关的要点。

- 参数用 `ARG:0,1,2...`（数值）或 `ARGS:0,1,2...`（字符串）
- 调用侧类型与定义侧不一致时报错
- 推荐用 `ARG`/`ARGS` 作形参名（而非直接用全局变量）

## CALL 系命令一览

详见 `references/commands/control-flow.md` 和 `references/core-concepts/user-defined-functions.md`。

### 参数引用传递

见 `references/core-concepts/user-defined-functions.md`。

## 函数属性

函数属性以 `#` 开头，必须写在函数声明的紧邻下一行。

### `#ONLY` — 事件函数独占

仅对事件函数有效。使用 `#ONLY` 后，同名事件函数中**仅执行第一个**，其余被跳过：

```
@EVENTFIRST
#ONLY
    ; 只执行这个 EVENTFIRST
```

## 预处理器条件块

以下为预处理器级别的条件块（不是运行时的 IF）。只能以整行为单位，同行不能附带其他代码。

### `[SKIPSTART]` ～ `[SKIPEND]` — 跳过代码块

预处理指令。`[SKIPSTART]` 到 `[SKIPEND]` 之间的所有行不会被执行。是否需要此指令取决于具体的兼容性需求，常规 Emuera 开发中不常用。

```
[SKIPSTART]
    ; 此处的代码不会被 Emuera 执行
[SKIPEND]
```

### `[IF XXX]` ～ `[ELSEIF]` ～ `[ELSE]` ～ `[ENDIF]` — 宏条件编译

根据某个宏 `XXX` 是否被 `#DEFINE` 定义来决定代码块是否被编译。**在 `.ERB` 和 `.ERH` 中均可使用**。

```
[IF FEATURE_X]
    ; FEATURE_X 宏已定义时编译此块
[ELSEIF FEATURE_Y]
    ; FEATURE_Y 宏已定义时编译此块
[ELSE]
    ; 以上宏均未定义时编译此块
[ENDIF]
```

这允许通过宏开关控制代码的包含/排除（如可选模块等）。

#### `ISDEFINED` 检查

`[IF XXX]` 本质上等价于 `ISDEFINED(XXX)` 检查——判断宏是否已被 `#DEFINE` 定义。也可以用显式写法：

```
[IF ISDEFINED(FEATURE_X)]
    ; 与 [IF FEATURE_X] 等效
[ENDIF]
```

#### 不支持表达式与式中函数

`[IF XXX]` 是预处理器指令，**在预处理器级别工作，不支持表达式运算或式中函数**。以下写法无效：

```
[IF VERSION >= 2]        ; ❌ 不支持表达式比较
[IF SOME_FUNC()]         ; ❌ 不支持式中函数调用
```

条件编译只能判断宏是否已定义（以及 `[IF_DEBUG]` / `[IF_NDEBUG]` 判断调试模式），不能进行数值比较或逻辑运算。

### `[IF_DEBUG]` / `[IF_NDEBUG]` — 调试模式条件

调试专用条件块，详见 `references/reference/debug.md`。**不推荐在开发中使用。**

## 宏的限制

- 宏的内容只能是表达式，**不能包含赋值运算符**（`=`、`+=` 等）。
- 宏中括号必须配对。
- 宏名不能替代命令名（如 `PRINT`）。
- 宏展开不能应用于 `.csv`、`.ERH` 本身、预处理器指令、属性名。
- ERH 中的 `#DIM` 元素数不会展开宏。
- 宏可多重展开（宏 A 引用宏 B），但存在循环引用时引擎会检测并报错。
- 空宏（`#DEFINE HOGE` 无替换内容）合法，用于 `[IF HOGE]` 条件判断。

### 宏展开范围

宏展开**只发生在式中**，不在命令参数字符串中展开：

```
#DEFINE FIVE 5

; 式中展开
X = FIVE              ; X = 5（展开）

; 命令参数不展开
PRINT FIVE             ; 打印 "FIVE"（不展开）
PRINTV FIVE            ; 打印 5（PRINTV 的参数是表达式，会展开）
```

### 宏的文本替换陷阱

由于宏是**纯文本替换**（不是值替换），多token宏在复合表达式中可能产生意外结果：

```
#DEFINE SIX 1 + 5
#DEFINE NINE 8 + 1

X = SIX * NINE
; 展开为：X = 1 + 5 * 8 + 1
; 结果：X = 42（而非 54）
; 因为 * 优先级高于 +
```

**解决方案**：用括号保护宏定义：

```
#DEFINE SIX (1 + 5)
#DEFINE NINE (8 + 1)

X = SIX * NINE
; 展开为：X = (1 + 5) * (8 + 1)
; 结果：X = 54
```

### 宏的典型用法

```
; 常量字符串
#DEFINE HOGE "ほげほげ"

; 变量别名
#DEFINE PIYO A

; 数组元素别名
#DEFINE FUGA DA:10

; 函数表达式
#DEFINE HOGERA LOCAL + MY_FUNC(X, Y)
```

## 预处理器

### `#DEFINE` / `#ENDDEFINE`

`#DEFINE` 是预处理阶段的纯文本替换——不是变量，展开发生在代码执行之前。只能写在 `.ERH` 中。

有两种形式：

**单行宏**（不带 `#ENDDEFINE`，替换为单个值）：
```
#DEFINE FIVE 5
```
之后代码中的 `FIVE` 会被替换为 `5`：`X = FIVE` → `X = 5`

**多行宏**（带 `#ENDDEFINE`，替换为代码块）：
```
#DEFINE MACRO_NAME
    PRINTL 宏被展开了
#ENDDEFINE
```

**带参数的宏**：
```
#DEFINE ADD_PRINT(a, b)
    PRINTFORML {a} + {b} = {a + b}
#ENDDEFINE
```

### 使用宏

在 ERB 代码中直接写宏名称即可展开。

### `#UNDEF` 取消宏定义

```
#UNDEF MACRO_NAME
```

取消之前用 `#DEFINE` 定义的宏。只能写在 `.ERH` 中。

取消后，该宏名不再被识别，`ISDEFINED(MACRO_NAME)` 返回 0，条件编译 `[IF MACRO_NAME]` 也不会命中。

### 空宏定义

```
#DEFINE MACRO_NAME
; 没有 #ENDDEFINE，没有宏体
```

空宏没有替换内容，在代码中引用时不会展开为任何文本。空宏的意义在于条件编译：

```
#DEFINE FEATURE_ENABLED

[IF FEATURE_ENABLED]
    ; 此块被编译
[ELSE]
    ; 此块被跳过
[ENDIF]
```

`ISDEFINED(FEATURE_ENABLED)` 返回 1，但因为宏体为空，代码中写 `FEATURE_ENABLED` 不会产生任何输出。

### 宏的循环参照

Emuera 会检测宏的自我参照和循环参照，防止无限展开：

```
#DEFINE A
    B
#ENDDEFINE

#DEFINE B
    A
#ENDDEFINE
; 这会导致无限展开，Emuera 会报错
```

同样，宏直接引用自身也会被检测：

```
#DEFINE A
    A
#ENDDEFINE
; 自我参照，也会报错
```

引擎在预处理阶段展开宏时会追踪展开链，一旦发现同一个宏在展开链中出现第二次，即判定为循环参照并报错终止。

## 预处理器指令一览

| 指令 | 说明 |
|------|------|
| `#DEFINE` / `#ENDDEFINE` | 定义宏（文本替换） |
| `#UNDEF` | 取消宏定义（只能写在 .ERH 中） |
| `#DIM` / `#DIMS` | 定义变量 |
| `#LOCALSIZE` / `#LOCALSSIZE` | 设定当前函数内 LOCAL/LOCALS 的元素个数上限 |
| `#FUNCTION` | 声明当前函数为式中函数（返回数值），必须用 `RETURNF` 返回 |
| `#FUNCTIONS` | 声明当前函数为式中函数（返回字符串），必须用 `RETURNF` 返回 |

> `#FUNCTION` 和 `#FUNCTIONS` 将普通 `@` 函数标记为式中函数，使其可被 `CALLF` 调用或直接写在表达式中。标记后函数必须用 `RETURNF <值>` 结束（不能用普通 `RETURN`）。`RETURNF` 省略参数时返回 `0` 或空字符串。

### 式中函数完整示例

```
; 定义数值型式中函数
@DOUBLE(value)
#FUNCTION
    RETURNF value * 2

; 定义字符串型式中函数
@GREET(name)
#FUNCTIONS
    RETURNF @"你好，{name}！"

; 调用
A = DOUBLE(5)        ; A = 10
STR:0 = GREET("小明") ; STR:0 = "你好，小明！"
```
