# 系统命令

## 游戏流程控制

### BEGIN

```
BEGIN TITLE       ; 回到标题画面
BEGIN FIRST       ; 开始新游戏
BEGIN SHOP        ; 进入商店阶段
BEGIN TRAIN       ; 进入调教阶段
BEGIN AFTERTRAIN  ; 进入调教后阶段（@EVENTEND）
BEGIN ABLUP       ; 进入能力提升阶段
BEGIN TURNEND     ; 进入回合结束阶段（@EVENTTURNEND）
```

### QUIT / RESTART / FORCE_QUIT

```
QUIT                      ; 退出到标题
RESTART                   ; 重启当前函数
FORCE_QUIT                ; 强制退出
FORCE_QUIT_AND_RESTART    ; 强制退出并重启
```

## 存档/读档

### SAVENOS

```
SAVENOS     ; 获取存档数（存入 RESULT）
```

## 变量操作（通过字符串名称读写变量）

以下命令允许通过**字符串**指定变量名来读写变量，适用于需要动态决定操作目标变量的场景。

### VARSET — 批量填充变量

```
VARSET 变量名 [, 值, 起始索引, 结束索引]
```

对指定变量的数组进行批量赋值。比 `FOR`-`NEXT` 循环快得多，适合数万次以上的批量操作。

| 参数 | 说明 |
|------|------|
| `变量名` | 目标变量（如 `FLAG`、`CFLAG:MASTER:0`），不可省略 |
| `值` | 要填入的值。省略时整数型填 `0`，字符串型填空字符串 |
| `起始索引` | 起始元素位置（包含）。省略时为 `0` |
| `结束索引` | 结束元素位置（**不包含**）。省略时填到末尾 |

- 对角色变量（如 `CFLAG:MASTER:0`），只影响指定角色的元素
- 对 `DITEMTYPE`、`TA` 等多维数组，第三参数起被忽略，始终填充全数组

```
; 清空所有 FLAG
VARSET FLAG, 0

; STR:0 到 STR:9 填入指定字符串
VARSET STR, "文本", 0, 10

; 清空 MASTER 的所有 CFLAG
VARSET CFLAG:MASTER:0, 0
```

### VARSETEX — 通过字符串名批量填充

```
int VARSETEX(变量名字符串, 值 [, 全维度, 起始, 结束])
```

与 `VARSET` 类似，但第一参数是**变量名的字符串**而非直接写变量标识符。命令形式和式中函数形式均支持，始终返回 `1`。

| 参数 | 说明 |
|------|------|
| `变量名字符串` | 变量的字符串名称，如 `"FLAG"`、`"整数配列2D:1:2"` |
| `值` | 要填入的值 |
| `全维度` | 非 `0` 或省略时填充所有维度；为 `0` 时只填充最低维 |
| `起始` | 起始元素位置（包含），省略为 `0` |
| `结束` | 结束元素位置（**不包含**），省略为数组末尾 |

```
; 通过字符串名填充一维数组
VARSETEX "整数配列", -1, 0, 3, 5
; 等价于 VARSET 整数配列, -1, 3, 5

; 填充二维数组的指定位置
VARSETEX "整数配列2D:1:2", -1, 0, 1
; 整数配列2D:1:2 位置起共 1 个元素填 -1
```

### CVARSET — 按角色范围批量设置

```
CVARSET 角色变量, 元素索引, 值 [, 起始角色ID, 结束角色ID]
```

对**指定范围的登记角色**，统一设置某个角色变量的某个元素。

| 参数 | 说明 |
|------|------|
| `角色变量` | 目标角色变量（如 `CFLAG`、`CSTR`） |
| `元素索引` | 要设置的元素编号。对 `NAME` 等一维角色变量，此参数被忽略 |
| `值` | 要填入的值。省略时整数型填 `0`，字符串型填空字符串 |
| `起始角色ID` | 起始角色登记编号（包含），省略为 `0` |
| `结束角色ID` | 结束角色登记编号（**不包含**），省略为 `CHARANUM` |

```
; 将全部角色 CFLAG:10 设为 123
CVARSET CFLAG, 10, 123
; 等价于：
; REPEAT CHARANUM
;     CFLAG:COUNT:10 = 123
; REND
```

### GETVAR / GETVARS / SETVAR — 字符串名读写单个变量

```
int GETVAR(变量名字符串)
str GETVARS(变量名字符串)
int SETVAR(变量名字符串, 值)
```

通过字符串名称读写变量/常量的值。命令形式和式中函数形式均支持。

- `GETVAR`：返回指定名称的整数型变量/常量的值
- `GETVARS`：返回指定名称的字符串型变量/常量的值
- `SETVAR`：将值赋给指定名称的变量（**常量不可赋值**，会报错）。值类型必须与变量类型匹配。始终返回 `1`

```
; 读取变量值
PRINTFORML {GETVAR("MONEY")}

; 动态构造变量名并读写
VARS 前缀 = "ITEM"
VARI 编号 = 5
VARS 变量名 = @"{前缀}{编号}"
SETVAR 变量名, 100
; 等价于 ITEM5 = 100（如果 ITEM5 存在才行，不存在会报错）
```

## 时间

### GETTIME

```
GETTIME     ; 获取当前系统时间（存入 RESULT）
```

### GETMILLISECOND / GETSECOND

```
GETMILLISECOND   ; 获取毫秒
GETSECOND        ; 获取秒数
```

## 内存

### GETMEMORYUSAGE

```
GETMEMORYUSAGE   ; 获取内存使用量（字节, 存入 RESULT）
```

### CLEARMEMORY

```
CLEARMEMORY      ; 尝试释放内存（返回释放量）
```

## 配置

### GETCONFIG / GETCONFIGS

```
GETCONFIG 配置名      ; 获取配置值（int）
GETCONFIGS 配置名     ; 获取配置值（string）
```

### SETCONFIG

```
SETCONFIG 配置名, 值  ; 设置配置值
```

### OUTPUTLOG

```
OUTPUTLOG 字符串                    ; 输出到默认日志文件
OUTPUTLOG 文件路径, 字符串          ; 输出到指定文件
```

输出日志到文件。支持指定子目录（但不能指定上级目录 `..`）。

- 无文件名参数时，输出到默认日志
- 带文件名参数时（第一参数为字符串），输出到指定路径
- 允许的扩展名由 `emuera.config` 中「LOADTEXTとSAVETEXTで使える拡張子」配置项决定（默认仅 `txt`）
- 日志编码为 Unicode

```
; 输出到默认日志
OUTPUTLOG "处理完成"

; 输出到指定文件
OUTPUTLOG "logs/debug.txt", "调试信息：{变量值}"
```

## 日志

### SKIPLOG

```
SKIPLOG 0   ; 显示日志
SKIPLOG 1   ; 隐藏日志
```

## 窗口操作

### SETANIMETIMER

```
SETANIMETIMER 间隔     ; 设置动画定时器
```

### WAIT / FORCEWAIT

```
WAIT                   ; 等待玩家点击
WAIT 毫秒数            ; 等待指定毫秒
FORCEWAIT 毫秒数       ; 强制等待（不可跳过）
```

### WAITANYKEY

```
WAITANYKEY             ; 等待任意按键
```

### TWAIT

```
TWAIT 毫秒数           ; 定时等待
```

### AWAIT

```
AWAIT                  ; 动画等待
```

### CLEARTEXTBOX

```
CLEARTEXTBOX           ; 清除文本框
```

### TEXTBOX 系

```
SETTEXTBOX 字符串      ; 设置文本框文字
GETTEXTBOX             ; 获取文本框文字
MOVETEXTBOX x, y, w    ; 移动/调整文本框
RESUMETEXTBOX          ; 恢复文本框默认位置
```

### REDRAW

```
REDRAW 0/1             ; 关闭/开启自动重绘
```

### UPDATECHECK

```
UPDATECHECK            ; 强制刷新检查
```

## 停止命令

### STOPCALLTRAIN

```
STOPCALLTRAIN          ; 停止自动调教
```

### STOPSOUND / STOPBGM

见音频命令。

## 窗口状态

### ISACTIVE

```
int ISACTIVE
```

检查 Emuera 窗口是否处于激活状态（前台窗口）。命令形式和式中函数形式均可使用。

- 返回 `1`：窗口处于激活状态（用户正在操作 Emuera）
- 返回 `0`：窗口处于非激活状态（用户切换到其他程序）

```
; 仅在窗口激活时执行自动操作
IF ISACTIVE
    CALL 自动保存
ENDIF
```
