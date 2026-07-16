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

### SAVEGAME / LOADGAME

```
SAVEGAME [存档槽号]   ; 存档
LOADGAME [存档槽号]   ; 读档
```

如省略槽号则使用标准存档画面。

### SAVEDATA / LOADDATA / RESETDATA

```
SAVEDATA 文件名 [, 版本号]     ; 保存变量数据
LOADDATA 文件名                ; 加载变量数据
RESETDATA                      ; 重置变量数据
```

### SAVEGLOBAL / LOADGLOBAL / RESETGLOBAL

```
SAVEGLOBAL      ; 保存全局变量到 global.sav
LOADGLOBAL      ; 从 global.sav 加载全局变量
RESETGLOBAL     ; 重置全局变量
```

### SAVECHARA / LOADCHARA

```
SAVECHARA 角色番号, 文件名     ; 保存单个角色
LOADCHARA 角色番号, 文件名     ; 加载单个角色
```

### SAVETEXT / LOADTEXT

```
SAVETEXT 文件名, 字符串     ; 保存文字列到文件
LOADTEXT 文件名, 变量       ; 从文件加载文字列
```

### CHKDATA / DELDATA

```
CHKDATA 文件名     ; 检查存档文件是否存在（0=无/1=有）
DELDATA 文件名     ; 删除存档文件
```

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

## 调试

### DEBUGPRINT

```
DEBUGPRINT 字符串     ; 仅在调试模式下输出
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
