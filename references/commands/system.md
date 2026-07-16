# 系统命令

## 游戏流程控制

### BEGIN

```
BEGIN TITLE    ; 回到标题画面
BEGIN FIRST    ; 开始新游戏
BEGIN SHOP     ; 进入商店阶段
BEGIN TRAIN    ; 进入调教阶段
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

## 变量操作

### VARSET / VARSETEX

```
VARSET 变量名, 值     ; 设置任意变量值（通过字符串）
VARSETEX 表达式        ; 复杂变量设置
```

### CVARSET

```
CVARSET 变量名, 值     ; 强制设置变量
```

### GETSETVAR

```
GETSETVAR 变量名, 索引, 值    ; 获取/设置变量
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
OUTPUTLOG 字符串      ; 输出到日志文件
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
