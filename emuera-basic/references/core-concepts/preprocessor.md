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

## 预处理器

### `#DEFINE` / `#ENDDEFINE`

定义宏：

```
#DEFINE MACRO_NAME
    PRINTL 宏被展开了
#ENDDEFINE
```

### `#DEFINE` 带参数

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
| `#DEFINE` / `#ENDDEFINE` | 定义宏 |
| `#UNDEF` | 取消宏 |
| `#DIM` / `#DIMS` | 定义变量 |
| `#LOCALSIZE` / `#LOCALSSIZE` | 设定 LOCAL/LOCALS 元素个数 |
| `#FUNCTION` | 声明式中函数开始 |
| `#DEFINE`（函数内） | 函数内宏 |
