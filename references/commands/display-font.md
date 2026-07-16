# 显示操作与字体操作

## 颜色操作

### SETCOLOR / RESETCOLOR

```
SETCOLOR r, g, b     ; 设置前景色（RGB 0-255）
SETCOLOR int         ; 设置前景色（0xRRGGBB 格式）
RESETCOLOR           ; 恢复默认前景色
```

### SETBGCOLOR / RESETBGCOLOR

```
SETBGCOLOR r, g, b   ; 设置背景色（RGB 0-255）
SETBGCOLOR int       ; 设置背景色（0xRRGGBB 格式）
RESETBGCOLOR         ; 恢复默认背景色
```

### SETCOLORBYNAME / SETBGCOLORBYNAME

```
SETCOLORBYNAME colorName   ; 使用颜色名设置前景色
SETBGCOLORBYNAME colorName ; 使用颜色名设置背景色
```

### GETCOLOR / GETBGCOLOR / GETDEFCOLOR / GETDEFBGCOLOR / GETFOCUSCOLOR

```
GETCOLOR               ; 返回当前前景色（int）
GETBGCOLOR             ; 返回当前背景色（int）
GETDEFCOLOR            ; 返回默认前景色（int）
GETDEFBGCOLOR          ; 返回默认背景色（int）
GETFOCUSCOLOR          ; 返回焦点色（int）
```

### COLOR_FROMNAME / COLOR_FROMRGB

```
COLOR_FROMNAME string  ; 颜色名 → 颜色值（int）
COLOR_FROMRGB r, g, b  ; RGB → 颜色名（string）
```

## 字体操作

### FONTBOLD / FONTITALIC / FONTREGULAR

```
FONTBOLD       ; 粗体
FONTITALIC     ; 斜体
FONTREGULAR    ; 常规
```

### FONTSTYLE

```
FONTSTYLE 0   ; 常规
FONTSTYLE 1   ; 粗体
FONTSTYLE 2   ; 斜体
FONTSTYLE 3   ; 粗斜体
```

### GETSTYLE

```
GETSTYLE       ; 返回当前字体样式（int）
```

### SETFONT / GETFONT / CHKFONT

```
SETFONT 字体名     ; 设置字体
GETFONT            ; 返回当前字体名（string）
CHKFONT 字体名     ; 检查字体是否可用（int，返回 0/1）
```

## 对齐操作

### ALIGNMENT

```
ALIGNMENT LEFT    ; 左对齐
ALIGNMENT CENTER  ; 居中
ALIGNMENT RIGHT   ; 右对齐
```

### CURRENTALIGN

```
CURRENTALIGN      ; 返回当前对齐方式（string："LEFT"/"CENTER"/"RIGHT"）
```

## 重绘控制

### REDRAW

```
REDRAW 0   ; 禁用自动重绘
REDRAW 1   ; 启用自动重绘
```

### CURRENTREDRAW

```
CURRENTREDRAW   ; 返回当前 REDRAW 状态（int）
```

## 显示信息参照

### PRINTCPERLINE / PRINTCLENGTH

```
PRINTCPERLINE    ; 返回每行可打印字符数（int）
PRINTCLENGTH     ; 返回当前行已使用的字符数（int）
```

### LINEISEMPTY

```
LINEISEMPTY      ; 返回当前行是否为空（int）
```

### BAR / BARSTR

```
BAR 当前值, 最大值, 长度    ; 绘制进度条
BARSTR 当前值, 最大值, 长度 ; 返回进度条字符串（string）
```

### MONEYSTR

```
MONEYSTR 金额 [, 选项]     ; 返回格式化金钱字符串
```

### SKIPDRW 相关

```
SKIPDRW       ; 开始跳过绘制区域
ENDSKIPDRW    ; 结束跳过绘制区域
```

### BAR 属性设置

```
SETBARSTYLE  style        ; 进度条样式
SETBARTEXT  text          ; 进度条文字
```

## 跳过控制

### SKIPDISP / ISSKIP / MOUSESKIP / MESSKIP

```
SKIPDISP 0   ; 终止跳过
SKIPDISP 1   ; 开始跳过
ISSKIP        ; 返回当前是否跳过中（int）
MOUSESKIP     ; 鼠标跳过状态
MESSKIP       ; 消息跳过状态
```

### NOSKIP / ENDNOSKIP

```
NOSKIP        ; 标记不可跳过区间开始
ENDNOSKIP     ; 标记不可跳过区间结束
```

## BITMAP 缓存

```
BITMAP_CACHE_ENABLE 1   ; 启用位图缓存
BITMAP_CACHE_ENABLE 0   ; 禁用它
```

## 其他显示命令

### SKIPLOG

```
SKIPLOG 0   ; 显示日志
SKIPLOG 1   ; 隐藏日志
```

### GETDISPLAYLINE

```
GETDISPLAYLINE 行号   ; 返回指定行的内容（string）
```
