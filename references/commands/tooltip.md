# TOOLTIP（工具提示）命令

TOOLTIP 系命令用于控制当鼠标悬停在按钮上时显示的提示文字。基础功能（`TOOLTIP_SETDELAY`、`TOOLTIP_SETDURATION`）内置于 Emuera，而扩展功能（`TOOLTIP_CUSTOM`、`TOOLTIP_SETFONT` 等）需要启用 `TOOLTIP_CUSTOM`。

## 基本设置

### TOOLTIP_SETDELAY — 悬停延迟

```
TOOLTIP_SETDELAY 毫秒
```

设置鼠标悬停后、提示文字出现之前的延迟时间。默认 `500` 毫秒，最大 `32767`。

### TOOLTIP_SETDURATION — 显示时长

```
TOOLTIP_SETDURATION 毫秒
```

设置提示文字显示的时间长度。设为 `0` 使用系统默认时长。

```
; 悬停 300ms 后显示，持续 2000ms
TOOLTIP_SETDELAY 300
TOOLTIP_SETDURATION 2000
```

## 扩展功能

以下功能需要先执行 `TOOLTIP_CUSTOM 1` 启用后才生效。设为 `0` 恢复默认行为。

### TOOLTIP_CUSTOM — 启用扩展

```
TOOLTIP_CUSTOM 1   ; 启用扩展功能
TOOLTIP_CUSTOM 0   ; 关闭扩展，恢复默认
```

### TOOLTIP_SETCOLOR — 提示文字颜色

```
TOOLTIP_SETCOLOR 文字颜色, 背景颜色
```

设置提示文字的前景色和背景色，均使用 `0xRRGGBB` 格式。

```
; 白色文字 + 深灰背景
TOOLTIP_SETCOLOR 0xFFFFFF, 0x333333

; 使用颜色名
TOOLTIP_SETCOLOR COLOR_FROMNAME("White"), COLOR_FROMNAME("DarkGray")
```

### TOOLTIP_SETFONT — 提示文字字体

```
TOOLTIP_SETFONT 字体名
```

设置提示文字使用的字体。

### TOOLTIP_SETFONTSIZE — 提示文字大小

```
TOOLTIP_SETFONTSIZE 字号
```

设置提示文字的字体大小。

### TOOLTIP_FORMAT — 文字对齐

```
TOOLTIP_FORMAT 格式标志
```

设置提示文字的对齐方式。参数值对应 .NET `TextFormatFlags` 枚举：

| 值 | 效果 |
|----|------|
| `0` | 默认（左对齐） |
| `1` | 水平居中 |
| `2` | 右对齐 |

```
; 居中显示
TOOLTIP_FORMAT 1
```

### HTML 标签支持

启用 `TOOLTIP_CUSTOM` 后，提示文字中可以使用 `<br>` 标签实现换行（未启用扩展时也可使用 `<br>`）。

---

## 完整设置示例

```
; 启用扩展并配置提示样式
TOOLTIP_CUSTOM 1
TOOLTIP_SETCOLOR 0x00AA00, 0x00FF00
TOOLTIP_SETFONT "Arial"
TOOLTIP_SETFONTSIZE 24
TOOLTIP_FORMAT 2

; 显示带提示的按钮
HTML_PRINT "<button value='1' title='第一行<br>第二行'>悬停查看提示</button>"

; 切换样式
TOOLTIP_SETCOLOR 0x333333, 0xAAAAAA
TOOLTIP_SETFONT "MS Gothic"
TOOLTIP_SETFONTSIZE 12
TOOLTIP_FORMAT 1

; 恢复默认
TOOLTIP_CUSTOM 0
```
