# 流程控制命令

## IF 条件判断

```
IF 条件式
    ; 条件成立时执行
ELSEIF 条件式
    ; 第二条件成立时执行
ELSE
    ; 全部不成立时执行
ENDIF
```

- 条件式：`0` = 非成立（false），非 `0` = 成立（true）
- `ELSEIF` 可省略，可多个
- `ELSE` 可省略

### SIF（单行 IF）

```
SIF 条件式
    命令
```

条件成立时执行下一行。不成立时跳过下一行。
Emuera 中空行和注释行被忽略后判断「下一行」。

## FOR 循环

```
FOR 变量, 开始值, 结束值 [, 步长]
    ;
NEXT
```

```
FOR LCOUNT, 0, CHARANUM
    PRINTFORML 角色{LCOUNT}
NEXT
```

步长默认为 1。

- 第1参数只能是普通数值变量，不能使用角色变量
- 步长为正时循环直到 `>= 结束值`；步长为负时循环直到 `<= 结束值`
- 步长为 0 时为无限循环
- 循环开始后各参数值被固定，中途修改不影响循环
- 用 GOTO 直接跳入 FOR 循环体内时 NEXT 会被忽略

## WHILE 循环

```
WHILE 条件式
    ;
WEND
```

## REPEAT 循环

```
REPEAT 次数
    ; COUNT:0 为当前循环计数（0 ～ 次数-1）
REND
```

```
REPEAT 10
    PRINTFORML 第{COUNT:0}次
REND
```

## SELECTCASE 多分支

```
SELECTCASE 式
CASE 值1
    ;
CASE 值2, 值3
    ;
CASE IS <= 30
    ;
CASE 10 TO 20
    ;
CASEELSE
    ;
ENDSELECT
```

- `CASE` 可多个，不会 fall-through（与 C 的 switch 不同）
- `BREAK` 不能跳出 SELECTCASE（因不 fall-through，无需 break）
- `CASEELSE` 可选（default 分支）
- 支持三种 CASE 格式：
  - 直接值：`CASE 0` 或 `CASE 1, 2, 3`
  - `IS <运算符> <式>`：如 `CASE IS <= 30`、`CASE IS > 100`（必须严格写为 `IS <运算符> <式>`，不支持 `30 < IS`）
  - `<式> TO <式>`：范围匹配，如 `CASE 10 TO 20`（不支持 `(10 TO 20) || (30 TO 40)` 的逻辑组合）
- IS/TO 格式中 TO 右值小于左值时永不匹配
- 多个条件使用短路求值
- 支持字符串表达式
- 用 GOTO 直接跳入 SELECTCASE 体内时，会先正常执行到 CASE/CASEELSE/ENDSELECT 前，再跳到 ENDSELECT 之后

## 跳转

### GOTO

```
GOTO 标签名
;
$标签名
    ; 跳转目标
```

不推荐过度使用，影响可读性。

### CONTINUE

在循环中使用，跳过当前迭代继续下一次：

```
FOR LCOUNT, 0, 10
    SIF LCOUNT == 5
        CONTINUE
    ; LCOUNT=5 时跳过这里
NEXT
```

### BREAK

在循环中使用，跳出循环。

## DO 循环

```
DO
    ;
LOOP WHILE 条件
```

```
DO
    ;
LOOP UNTIL 条件
```

## 函数控制

### CALL

```
CALL 函数名 [, 参数1, 参数2, ...]
```

执行函数，完成后返回调用处。

### JUMP

```
JUMP 函数名 [, 参数1, 参数2, ...]
```

跳转到函数，**不返回**。

### RETURN

```
RETURN [值]
```

从函数返回。可带返回值（赋值给 `RESULT`）。

### CALLF / CALLFORMF

```
CALLF 式中函数名 [, 参数]
CALLFORMF 式中函数名 [, 参数]
```

调用式中函数（内联函数）。

### TRY / CATCH / ENDCATCH / THROW

```
TRY
    ; 可能出错的代码
CATCH
    ; 出错时的处理
ENDCATCH

THROW 错误信息
```

异常处理机制。

### TRYCALL / TRYCALLF / TRYCALLFORMF / TRYCALLFORMF

```
TRYCALL 函数名 [, 参数]
; 带 TRY-CATCH 的调用
```

### CALLEVENT

```
CALLEVENT 事件函数名
```

调用事件函数（触发所有多重定义）。

### CALLTRAIN / STOPCALLTRAIN

```
CALLTRAIN      ; 开始自动调教
STOPCALLTRAIN  ; 停止自动调教
```

### CALLSHARP

```
CALLSHARP 函数名字符串 [, 参数]
```

通过字符串动态调用函数。

## BEGIN

```
BEGIN TITLE    ; 回到标题
BEGIN FIRST    ; 开始新游戏
BEGIN SHOP     ; 进入商店
BEGIN TRAIN    ; 进入调教
```

切换游戏阶段，中断当前执行。

## FORCE 系

```
FORCE_BEGIN     ; 强制执行 BEGIN
FORCE_QUIT      ; 强制退出
FORCE_QUIT_AND_RESTART  ; 强制退出并重启
```

## RESTART

```
RESTART
```

回到当前函数开头重新执行。

## ASSERT

```
ASSERT 条件式
```

条件不成立时弹出错误信息（调试命令，详见 `references/reference/debug.md`）。
