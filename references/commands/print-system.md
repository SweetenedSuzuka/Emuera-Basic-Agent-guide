# PRINT 系统命令

PRINT 系命令是 ERA Basic 中最核心的输出命令群。

## PRINT 基本形

```
PRINT[(V|S|FORM|FORMS)][(K|D)][(L|W|N)]
```

### 第一括号：参数类型

| 后缀 | 参数类型 | 说明 |
|------|----------|------|
| （无） | `<字符串>` | 字符串直接指定 |
| `V` | `<数式>` | 数值输出 |
| `S` | `<字符串表达式>` | 字符串式 |
| `FORM` | `<格式化字符串>` | 格式化字符串 |
| `FORMS` | `<格式化字符串表达式>` | 格式化字符串式 |

### 第二括号：绘制选项

| 后缀 | 说明 |
|------|------|
| （无） | `FORCEKANA` 无效，`SETCOLOR` 有效 |
| `K` | `FORCEKANA` 应用 |
| `D` | `SETCOLOR` 无效（默认色） |

K 和 D 不可同时指定。

### 第三括号：换行与 WAIT

| 后缀 | 说明 |
|------|------|
| （无） | 纯打印，不换行不 WAIT |
| `L` | 打印后换行 |
| `W` | 打印后换行并 WAIT |
| `N` | 打印后不换行但 WAIT（Emuera.NET 专用，不能与 K/D 同时使用） |

### 组合示例

```
PRINT 你好           ; 基本
PRINTV MONEY               ; 数值
PRINTS STR:0               ; 字符串
PRINTL 打印一行             ; 打印 + 换行
PRINTW 打印一行并等待     ; 打印 + 换行 + WAIT
PRINTFORML {MONEY}元       ; 格式化 + 换行
PRINTFORMSW %STR:0%        ; 格式化字符串 + 换行 + WAIT
PRINTVK MONEY              ; 数值 + FORCEKANA 适用
PRINTFORMDL {MONEY}        ; 格式化 + 默认色 + 换行
```

## 各命令详细

### PRINTSINGLE

```
PRINTSINGLE(|V|S|FORM|FORMS)(|K|D) 字符串
```

PRINT 但将多个字符视为一个字符单元（用于处理全半角混合时的对齐）。

### PRINTC / PRINTLC

```
PRINTC(|K|D) 字符串
PRINTLC(|K|D) 字符串
```

居中打印。`PRINTLC` 还会换行。

### PRINTDATA

```
PRINTDATA(|K|D)(|L|W)
```

打印当前选中角色的数据（无参数）。

### PRINTBUTTON

```
PRINTBUTTON(|C|LC) 字符串, 返回值
```

打印可点击的按钮。

- `C` / `LC`：居中显示和换行
- 返回值在按钮被点击时变为 `RESULT`

```
PRINTBUTTON 是, 1
PRINTBUTTON 否, 0
```

### PRINTPLAIN

```
PRINTPLAIN(|FORM) 字符串
```

纯文本打印，不进行 HTML 解析。

### DRAWLINE / CUSTOMDRAWLINE

```
DRAWLINE                           ; 绘制分隔线（使用 _replace.csv 定义）
CUSTOMDRAWLINE 字符串               ; 自定义分隔线
```

### GETLINESTR

```
GETLINESTR 字符串
```

返回一行格式化后的字符串（用于尺寸计算等）。

### REUSELASTLINE

```
REUSELASTLINE 字符串
```

重新显示上一行内容（追加指定字符串）。

### CLEARLINE

```
CLEARLINE 行数
```

清除指定行数的输出。

### PRINT_STATUS 系

```
PRINT_ABL 数值     ; 打印能力值
PRINT_TALENT 数值  ; 打印素质
PRINT_MARK 数值    ; 打印刻印
PRINT_EXP 数值     ; 打印经验
PRINT_PALAM 数值   ; 打印参数
PRINT_ITEM         ; 打印物品一览
PRINT_SHOPITEM     ; 打印商店物品
```

### PRINT_IMG

```
PRINT_IMG src
PRINT_IMG src, width, height, ypos
PRINT_IMG src, srcb, width, height, ypos
PRINT_IMG src, srcb, srcm, width, height, ypos
```

显示图像。

| 参数 | 说明 |
|------|------|
| `src` | 通常状态的图片文件路径 |
| `srcb` | 鼠标悬停时显示的图片（可选） |
| `srcm` | 蒙版图。鼠标悬停时，光标下方蒙版图颜色（RGB 部分）会存入 `RESULT:3`（`INPUTMOUSEKEY` 时为 `RESULT:6`） |
| `width` | 图像宽度 |
| `height` | 图像高度 |
| `ypos` | 纵向偏移 |

### PRINT_RECT

```
PRINT_RECT 值
PRINT_RECT x, y, width, height
```

显示矩形。

### PRINT_SPACE

```
PRINT_SPACE 值
```

打印指定数量的空格。

### px 像素指定

`PRINT_IMG`、`PRINT_RECT`、`PRINT_SPACE` 的整数型参数后面可以追加 `px`（大小写不敏感），表示按像素指定。不加 `px` 则按字体大小的百分率解释。

```
PRINT_IMG "icon", (48+A) px, 48 px
PRINT_RECT 0, 0, 300 px, 200 px
PRINT_SPACE 200 px
```

### PRINTN 系（Emuera.NET 专用）

```
PRINTN(...)
PRINTVN(...)   ; 不换行但 WAIT
```

## FORCEKANA 说明

`FORCEKANA` 命令设定假名表示模式，影响带 `K` 后缀的 PRINT 命令：

```
FORCEKANA 0    ; 禁用
FORCEKANA 1    ; 半角片假名 → 全角片假名
FORCEKANA 2    ; 半角片假名 → 全角平假名
```

## 格式化字符串语法

```
{变量名}        ; 展开变数值
%变量名%        ; 展开字符串变量
{变量名, 格式}  ; 格式化数值（如 D 为十进制）
```

```
PRINTFORML 当前{MONEY}元、第{DAY}天
PRINTFORML 名字是%NAME:MASTER%
```
