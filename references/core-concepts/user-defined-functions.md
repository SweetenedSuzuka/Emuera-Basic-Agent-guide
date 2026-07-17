# 用户定义函数

ERB 中用户可以定义两种函数：**一般函数**（通过 `CALL`/`JUMP` 调用）和**式中函数**（可在表达式中直接使用）。

> 本章整合了 `Emuera/function.md` 和 `Emuera/user_defined_in_expression_function.md` 的内容。`#FUNCTION`/`#FUNCTIONS` 属性也记录在此，`preprocessor.md` 中仅有简要索引。

## 一般函数的定义与调用

### 基本形式

```
; 定义
@函数名
    ; 处理
    RETURN
```

```
; 调用
CALL 函数名
JUMP 函数名     ; 跳转到函数，不返回
```

### 带参数的函数

函数通过 `ARG`（整数参数）和 `ARGS`（字符串参数）接收参数，参数数量由函数的声明行决定。

**函数定义端：**

```
@函数名, ARG:0, ARGS:0
    ; ARG:0 — 第一个整数参数
    ; ARGS:0 — 第一个字符串参数
    RETURN
```

**调用端：**

```
; 变量传参
CALL FOOBAR, X, STR:0

; 常量传参
CALL FOOBAR, 123, "字符串"

; 格式化字符串传参
CALL FOOBAR, 123, @"[{COUNT}] 文本"

; 表达式传参
CALL FOOBAR, X + 10, "字符串" * 3

; 省略参数 — 全部省略
CALL FOOBAR

; 省略参数 — 仅第一参数
CALL FOOBAR, , "字符串"

; 省略参数 — 仅第二参数
CALL FOOBAR, 123
```

**错误示例：**

```
; 错误：参数过多
CALL FOOBAR, X, STR:0, Y

; 错误：类型不匹配 — 整数型参数传了字符串
CALL FOOBAR, "字符串", "字符串"

; 错误：类型不匹配 — 字符串型参数传了整数
CALL FOOBAR, 123, 456
```

> 参数类型不匹配会直接报错（不自动转换）。如需将数值传给字符串参数，请使用 `TOSTR` 函数。

### 参数传递机制

- 参数**值传递**（默认）：修改 `ARG`/`ARGS` 不会影响调用端的原始变量
- 省略参数时：数值型默认 `0`，字符串型默认空字符串
- 参数除了 `ARG`/`ARGS`，也可以是私有变量（用 `#DIM`/`#DIMS` 定义）
- 不建议将 `A`、`STR` 等全局变量作为参数（可读性差，且不能设初始值、不能省略）

### 参数的初始值

```
; 定义端指定初始值（只能用常量/常量字符串）
@函数名, ARGS:0 = "默认文本", ARG:0 = 111, ARG:1, ARG:2 = 200
    ; ...

; 调用端省略参数时使用初始值
CALL 函数名          ; ARG:0=111, ARGS:0="默认文本"
CALL 函数名, 50      ; ARG:0=50, ARGS:0="默认文本"
```

- 初始值只能是**常量**或**常量字符串**，不能是变量
- `ARG`/`ARGS`/私有变量可以设初始值，`A`/`STR` 等全局变量即使设了也会被忽略

### 引用传递（通过 `REF` 变量）

可以在函数的形参中使用 `#DIM REF` 变量来实现引用传递：

```
@TEST(HOGE)
#DIM REF HOGE
    HOGE = 100
    RETURN

@SYSTEM_TITLE
    A = 0
    CALL TEST(A)
    PRINTFORML A == {A}     ; 输出：A == 100
    B = 1
    CALL TEST(B)
    PRINTFORML B == {B}     ; 输出：B == 100
    WAIT
```

- 形参用 `#DIM REF`（或 `#DIMS REF`）声明
- 函数内修改 `REF` 变量会直接修改调用端传入的原始变量

## 特殊系统函数

以下函数由引擎在特定时机自动调用：

| 函数 | 调用时机 | 说明 |
|------|----------|------|
| `@EVENTLOAD` | 数据加载后 | 事件函数，可多重定义。未定义时转到 `@SHOW_SHOP`。 |
| `@TITLE_LOADGAME` | 标准标题画面选择「读档」 | 用于自定义标题画面的读档流程。若定义了 `@SYSTEM_TITLE`，需手动 `CALL`。 |
| `@SYSTEM_AUTOSAVE` | 自动存档时 | 用于自定义自动存档内容。 |
| `@SYSTEM_TITLE` | CSV 加载完成 / `BEGIN TITLE` 时 | 用于自定义标题画面。未定义时使用标准标题画面。 |
| `@CALLTRAINEND` | `CALLTRAIN` 自动执行结束后 | 非事件函数，**不能多重定义**。 |

## 事件函数属性

### `#ONLY`

```
@EVENTFIRST
#ONLY
    ; 有此属性时，只执行这一个 @EVENTFIRST
```

- 仅用于事件函数
- 指定 `#ONLY` 后，**只有这一个**同名事件函数被执行，其他都被忽略
- 多个同名事件函数都写 `#ONLY` 时，只执行最先遇到的那个

### `#PRI` / `#LATER` / `#SINGLE`

这些是 Emuera 的事件函数属性，**只能用于事件函数**。三者**不能与 `#ONLY` 并用**（并用时 `#PRI`/`#LATER`/`#SINGLE` 被忽略）。

```
; #PRI — 优先执行（在其他同名事件函数之前）
@EVENTFIRST
#PRI
    ; ...

; #LATER — 最后执行（在其他同名事件函数之后）
@EVENTFIRST
#LATER
    ; ...

; #SINGLE — 条件性中断执行
@EVENTFIRST
#SINGLE
    ; 如果以 RETURN 1 结束，则中断后续同名事件函数的调用
    ; 如果 RETURN 0 或其他值，后续函数仍会执行
    ; 中断按 #PRI 组 / 无属性组 / #LATER 组独立进行
    ; 不能与 #ONLY 并用
```

> ⚠️ `#PRI` 和 `#LATER` 同时标注在同一函数上会导致该函数**被调用两次**（一次优先 + 一次最后），注意避免。

## 式中函数（内联函数）

### 概念

式中函数是在**表达式中直接调用**的函数。有两种：

- **内置式中函数**：引擎内置，如 `ABS(X)`、`STRLENS(S)`（详见 `in-expression-functions.md`）
- **用户定义式中函数**：用 `@` 定义并加 `#FUNCTION`/`#FUNCTIONS` 属性

> ⚠️ "式中函数"与 Python/JavaScript 的匿名函数（lambda）毫无关系。在 ERB 中它只是一个可以在表达式里调用的普通 `@` 函数。

### 定义用户定义的式中函数

```
; 返回数值的式中函数
@DOUBLE(value)
#FUNCTION
    RETURNF value * 2

; 返回字符串的式中函数
@GREET(name)
#FUNCTIONS
    RETURNF @"你好，{name}！"
```

**关键规则：**

| 属性 | 返回值类型 | 结束方式 |
|------|-----------|----------|
| `#FUNCTION` | 整数 | `RETURNF <数值表达式>` |
| `#FUNCTIONS` | 字符串 | `RETURNF <字符串表达式>` |

- `RETURNF` 省略参数时返回 `0`（数值型）或空字符串（字符串型）
- 不能用普通 `RETURN` 结束
- 函数名前面仍要写 `@`

### 调用式中函数

```
; 通过 CALLF / CALLFORMF 调用
CALLF DOUBLE, 5
RESULT = RESULT           ; RESULT 获得 10

; 直接在表达式中使用
A = DOUBLE(5)             ; A = 10
STR:0 = GREET("世界")     ; STR:0 = "你好，世界！"

; 带异常处理的调用
TRYCALLF DOUBLE, 5
TRYCALLFORMF GREET, "世界"
```

### 式中函数的限制

- **不能**通过普通 `CALL` 调用（只能用 `CALLF`/`CALLFORMF` 或表达式内调用）
- **不能**使用 `WAIT`、`PRINT` 等会产生副作用的命令（只做计算并返回值）
- **不能**覆盖内置式中函数（不能定义名为 `ABS`、`MAX` 等的式中函数）
- **不支持**函数重载（同名却不同参数数量的多个式中函数）
- **受短絡評価影响**：当式中函数出现在 `&&`/`||` 的表达式里，如果整个表达式已经能确定结果，函数可能不会被调用

```
; 短絡評価示例
A = 0
X = (A != 0) && (DOUBLE(A) != 0)
; DOUBLE 不会被调用，因为 A!=0 已经为假
```

> ⚠️ 这意味着**不要在式中函数里写有副作用的代码**（如修改全局变量），因为它可能因为短絡評価而根本没有执行。

## CALL 系命令总览

| 命令 | 说明 |
|------|------|
| `CALL` | 调用一般函数（`@` 定义的普通函数） |
| `JUMP` | 跳转到函数，不返回 |
| `CALLF` | 调用式中函数（数值型） |
| `CALLFORMF` | 调用式中函数（字符串型） |
| `TRYCALL` | 带异常处理的 CALL |
| `TRYCALLF` | 带异常处理的 CALLF |
| `TRYCALLFORMF` | 带异常处理的 CALLFORMF |
| `TRY` / `CATCH` / `ENDCATCH` / `THROW` | 异常处理块 |

> 其他与函数调用相关的扩展命令（`CALLEVENT`、`CALLTRAIN`、`CALLSHARP` 等）详见 `emee-extensions/emee-summary.md` 和各自主命令文档。

## 函数定义最佳实践

1. **参数优先使用 `ARG`/`ARGS`**，不要用 `A`/`STR` 等全局变量做参数
2. **参数类型要一致**：不能混用数值/字符串类型。如需类型转换，调用端先 `TOSTR`/`TOINT`
3. **初始值只放常量**：`CALL FOOBAR` 时用到的默认值应是常量，不是变量
4. **式中函数不要有副作用**（因为短絡評価可能导致不执行）
5. **事件函数可以多重定义**（引擎会依次执行），普通函数不能重名
6. **ERB 所有函数平级**，没有类/模块/作用域，同一函数名在所有文件中只能定义一次
