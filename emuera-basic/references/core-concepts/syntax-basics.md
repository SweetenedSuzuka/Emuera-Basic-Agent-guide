# ERB 语法基础

本文档说明 ERA Basic（ERB）文件的基本书写规则。

## 文件配置

- ERB 文件放在 `ERB` 文件夹内
- 扩展名为 `.ERB`
- 文件名任意
- 使用文本编辑器编辑

## 基本规则

### 注释

```
; 从第 1 个字符为半角 ; 的整行被忽略
```

```
; 设定金钱
MONEY = 500
;游戏开始
PRINT 怎么办？
```

### 空行

空行被忽略。

### 行头空白

行头的半角空格和 Tab 被忽略。

### 不要行尾注释符号

```
; 错误
MONEY = 500;
TIME = 5;（从第5天开始）
```

### 半角输入

数值、命令、变量名、函数名等必须使用半角：

```
; 正确
MONEY = 500
PRINT 开始游戏。

; 错误
ＭＯＮＥＹ ＝　５００
ＰＲＩＮＴ 开始游戏。
```

### 字符串

**不要**用 `""` 括起字符串：

```
; 正确
PRINT 一天结束了……

; 错误（eramaker 会异常）
PRINT "一天结束了……"
```

### 一行完整

一个命令必须在一行内完整：

```
; 正确
PRINT 名雪的身体很敏感而且经验丰富，所以即使稍微粗暴一点也会愉悦。

; 错误（换行）
PRINT
名雪的身体很敏感而且经验丰富，所以即使稍微粗暴一点也会愉悦。
```

### 行连接（Emuera 扩展）

使用 `{` `}` 可以将多行连接为一行：

```
{
    #DIM CONST HOGE =
        1,2,3,4
}
; 等价于 #DIM CONST HOGE = 1,2,3,4
```

## 变量与赋值

### 基本赋值

```
MONEY = 500
```

`=` 前后用半角空格或 Tab 分隔。

### 计算赋值

```
MONEY = 15 + 34 + 25
MONEY = 150 + (100 - 50) * 10
MONEY = TIME * 10
MONEY = DAY % 7 * 10
```

整数运算，结果向下取整：`10 / 3 → 3`

### 复合赋值

```
MONEY += 50        ; MONEY = MONEY + 50
MONEY *= 7 - 2     ; MONEY = MONEY * 5
MONEY %= TIME      ; MONEY = MONEY % TIME
```

### 不要使用小数

```
; 错误
MONEY = 3.14
```

## 数组

```
; 一维数组
FLAG:0 = 0
FLAG:2 = 10
FLAG:35 = 440

; 使用变量作索引
FLAG:A += 100 / FLAG:2
FLAG:(FLAG:20) = 10000

; 错误
FLAG：0 = 10          ; 全角冒号
FLAG : 52 = 1000      ; 冒号前后有空格
```

数组索引从 `0` 开始，最大值由变量决定。

## 角色数组（二维数组）

```
(变量名):(角色编号):(变量编号)
```

```
ABL:MASTER:体力 = 10
CFLAG:TARGET:0 = 1
```

## 函数定义

```
; 事件函数（可多重定义）
@EVENTFIRST
    ; 游戏开始时的处理
    PRINTL 游戏开始！

; 一般函数
@FUNC001
    ; 处理
    RETURN

; 带参数的函数
@FUNC002, ARG:0, ARG:1, ARG,1100
    LOCAL = ARG:0 * ARG:1 / 100
    RETURN LOCAL

; 调用
CALL FUNC001
CALL FUNC002, 50, 200
```

## IF 条件

```
; IF 块
IF condition
    ;
ELSEIF condition2
    ;
ELSE
    ;
ENDIF

; 单行 IF
SIF condition
    PRINT 条件成立
```

条件式：`0` 表示非成立，非 `0` 表示成立。

## 循环

```
; FOR 循环
FOR LCOUNT, 0, CHARANUM
    ; LCOUNT 从 0 到 CHARANUM
NEXT

; WHILE 循环
WHILE FLAG:0 < 10
    FLAG:0 += 1
WEND

; REPEAT 循环
REPEAT 10
    ; COUNT:0 为当前计数（0～9）
REND
```

## SELECTCASE

```
SELECTCASE expression
CASE 0
    ;
CASE 1, 2
    ;
CASEELSE
    ;
ENDSELECT
```
