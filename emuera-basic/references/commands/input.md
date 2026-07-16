# 输入命令

## INPUT（数值输入）

```
INPUT [提示字符串]
```

等待玩家输入数值，结果存入 `RESULT`。

```
INPUT 请输入数值：
IF RESULT >= 0
    ; 处理输入
ENDIF
```

## INPUTS（字符串输入）

```
INPUTS [提示字符串]
```

等待玩家输入字符串，结果存入 `RESULTS`。

```
INPUTS 请输入名字：
STR:0 = RESULTS
```

## ONEINPUT

```
ONEINPUT [提示字符串]
```

等待单次按键输入，结果存入 `RESULT`。

## TINPUT（限时输入）

```
TINPUT 超时毫秒数 [, 提示字符串]
```

限时数值输入。超时则 `ISTIMEOUT` 变为 1，`RESULT` 为超时前的输入值。

```
TINPUT 5000, 5秒内输入数字：
SIF ISTIMEOUT
    PRINTL 超时！
```

## TONEINPUT

```
TONEINPUT 超时毫秒数 [, 提示字符串]
```

限时单键输入。

## INPUTMOUSEKEY

```
INPUTMOUSEKEY
```

等待鼠标输入：

- `RESULT = 0`：左键点击
- `RESULT = 1`：右键点击

## FLOWINPUT

```
FLOWINPUT
```

流程输入（持续输入处理）。

## GETKEY

```
GETKEY
```

获取当前按键状态（不阻塞）。

## MOUSEB

```
MOUSEB
```

获取鼠标按键状态（0=未按下 / 1=按下）。

## MOUSEXY

```
MOUSEX / MOUSEY
```

获取鼠标坐标。

## GETNUM

```
GETNUM 字符串, 目标变量名
```

从字符串中提取数值。

## BINPUT

```
BINPUT
```

按钮输入。
