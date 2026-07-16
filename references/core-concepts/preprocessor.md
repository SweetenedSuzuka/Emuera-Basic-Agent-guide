# 函数与预处理器

## 特殊系统函数

### `@EVENTLOAD`
加载存档后立即调用。可多重定义（事件函数）。
未定义时跳转至 `@SHOW_SHOP`（与 eramaker 行为相同）。

### `@TITLE_LOADGAME`
标准标题画面选择「加载」时调用。
可自定义标题画面的读档界面。

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

**函数定义侧**：
```
@函数名, 参数1, 参数2, ...
    参数用 ARG:0,1,2...（数值）或 ARGS:0,1,2...（字符串）
    函数中 #DIM/#DIMS 定义的私有变量也可作参数
```

**调用侧**：
```
CALL 函数名, 参数1, 参数2, ...
```

### 参数类型

- 数值：数值表达式或字符串表达式
- 字符串字面量：`"～～"` 括起来
- 格式化字符串字面量：`@"～～"` 

```
; 定义
@FOOBAR, ARG:0, ARGS:0
    ; 处理

; 调用
CALL FOOBAR, X, STR:0                   ; 变量指定
CALL FOOBAR, 123, "示例"               ; 常量指定
CALL FOOBAR, 123, @"[{COUNT}] 示例"    ; 格式化字符串
CALL FOOBAR, X + 10, "示例" * 10       ; 表达式指定
CALL FOOBAR                              ; 全部省略
CALL FOOBAR, , "示例"                   ; 省略第1参数
CALL FOOBAR, 123                         ; 省略第2参数
```

### 参数省略

省略参数时：数值型默认为 `0`，字符串型默认为空字符串（无初始值时）。

### 参数初始值

```
@函数名, 参数1 = 初始值1, 参数2 = 初始值2, ...
```

```
@FOOBAR, ARG:0 = 10, ARGS:0 = "默认值"
    ; ARG:0 默认 10，ARGS:0 默认 "默认值"
```

### 参数类型检查

v1.808 起，调用侧和定义侧类型不一致时报错。
要传入数值给字符串型参数需用 `TOSTR` 转换或修改配置。

### 推荐与非推荐写法

**推荐**：
```
@FOOBAR, ARG:0, ARG:1
```

**非推荐**（可读性差）：
```
@FOOBAR, X, Y            ; 直接用变量作参数
@FOOBAR, ARG:X, ARG:Y    ; 可变参数
@FOOBAR, ARG:0, ARG:(ARG:0) ; 嵌套
```

## CALL 系命令一览

| 命令 | 说明 |
|------|------|
| `CALL` | 调用函数，执行后返回 |
| `JUMP` | 跳转到函数（不返回） |
| `CALLF` | 调用式中函数 |
| `CALLFORMF` | 调用式中函数（格式化） |
| `TRYCALL` / `TRYCALLF` / `TRYCALLFORMF` | 带异常处理的调用 |
| `CALLTRAIN` | 调教自动执行 |
| `CALLEVENT` | 调用事件函数 |
| `CALLSHARP` | 通过字符串调用函数 |

### 参数引用传递（v1.810+）

使用 `#DIM REF` 声明引用类型变量作为参数，可以在函数内部修改调用侧传入的变量值：

```
@TEST(HOGE)
#DIM REF HOGE
    HOGE = 100
    RETURN

; 调用
A = 0
CALL TEST(A)
; 调用后 A = 100
```

引用参数的声明必须写在函数定义的紧邻下一行。

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

Emuera 忽略 `[SKIPSTART]` 到 `[SKIPEND]` 之间的所有行。用于兼容 eramaker：

```
[SKIPSTART]
    ; eramaker 专用代码，Emuera 直接跳过
[SKIPEND]

;!;[SKIPSTART]
    ; 只有 Emuera 执行的代码
;!;[SKIPEND]
```

### `[IF XXX]` ～ `[ELSEIF]` ～ `[ELSE]` ～ `[ENDIF]` — 宏条件编译

根据某个宏 `XXX` 是否被 `#DEFINE` 定义来决定代码块是否被编译：

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

### `#UNDEF`

取消宏定义。

## 预处理器指令一览

| 指令 | 说明 |
|------|------|
| `#DEFINE` / `#ENDDEFINE` | 定义宏（文本替换） |
| `#UNDEF` | 取消宏 |
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
