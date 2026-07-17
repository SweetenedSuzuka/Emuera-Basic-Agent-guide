# HTML_PRINT 标签系统

`HTML_PRINT` 是使用类似 HTML 的标签语法进行输出的命令。
Emuera 的 HTML 支持**不是**真正的 HTML，只是能够解析部分 HTML 标签。
除非特殊场景，否则不建议使用 `HTML_PRINT`，因为一旦使用，则这一行只会剩下 `HTML_PRINT` 的输出，无法与 `PRINT` 和 `PRINTFORM` 等Emuera 指令混合使用。

## 基本用法

```
HTML_PRINT 字符串表达式[, 换行控制]
```

- 第一参数是**字符串表达式**（与 `PRINTS` 相同，而非 `PRINT` 的纯字符串）
- 第二参数（整数型，可选，默认 `0`）：非 `0` 时不强制换行，后续 `HTML_PRINT` 可以继续在同一行输出
- 省略第二参数或为 `0` 时，执行后自动换行（行为接近 `PRINTSL`）

> **重要**：`HTML_PRINT` 不受 `ALIGNMENT`、`SETFONT`、`SETCOLOR`、`FONTSTYLE` 等命令的影响。所有样式必须通过标签属性指定。

标签格式：`<标签名 属性='值'>文字</标签名>`

属性值用 `'...'` 或 `"..."` 包围，推荐使用单引号（与 ERB 字符串区分）。

## 标签参考

### `<p>` — 段落对齐

```
<p align='left|center|right'>文字</p>
```

`align` 属性为必填，相当于 `ALIGNMENT` 命令的效果。`</p>` 可省略。

### `<nobr>` — 禁止自动换行

```
<nobr>文字</nobr>
```

相当于 `PRINTSINGLE` 命令。文字不会因超出绘制区域而自动换行（超出部分不可见），但 `<br>` 的显式换行仍然有效。`</nobr>` 可省略。

### `<br>` — 显式换行

插入换行。注意：多个 `<br>` 在 `CLEARLINE` 或 `LINECOUNT` 中仍视为一行。

### `<button>` / `<nonbutton>` — 按钮

```
<button value='数字' title='提示' pos='位置%'>文字</button>
<nonbutton title='提示' pos='位置%'>文字</nonbutton>
```

- `value`：**仅 `<button>` 可用**。按钮对应的数值（点击后该值进入 `RESULT`）。省略时按钮不响应点击（等同于 `<nonbutton>`）。
- `title`：鼠标悬停时显示的提示文字。
- `pos`：仅当 `align='left'` 且启用 `<nobr>` 时可用。距左端的位置，以字体大小的百分比表示。例如 `pos='300'` 相当于 3 个全角空格的位置。

### `<font>` — 字体与颜色

```
<font face='字体名' color='颜色' bcolor='选中色'>文字</font>
```

可嵌套使用。

- `face`：字体名。空字符串表示使用配置中指定的字体。字体不存在时回退到 "Microsoft Sans Serif"。
- `color`：文字颜色。格式为 `#FF0080`（十六进制）或 `red`、`blue` 等颜色名（支持 .NET `Color` 结构体的已定义颜色，不支持 `Transparent`）。
- `bcolor`：按钮被选中时的显示颜色。

### `<b>` / `<i>` / `<u>` / `<s>` — 文字样式

```
<b>粗体</b>  <i>斜体</i>  <u>下划线</u>  <s>删除线</s>
```

### `<img>` — 行内图片

```
<img src='资源名' srcb='选中时资源名' srcm='遮罩资源名' height='高' width='宽' ypos='纵向偏移'>
```

- `src`：**必填**。通过 `resources/` 文件夹的 CSV 定义的资源名。
- `srcb`：按钮选中时显示的图片资源名。省略时与 `src` 相同。
- `srcm`：鼠标悬停时的按钮遮罩图像资源名（类似 CBG 系的按钮贴图）。当执行 `INPUT` 系的鼠标模式或 `INPUTMOUSEKEY` 时，鼠标光标下方该遮罩图的颜色值（RGB）会存入 `RESULT:3`（`INPUTMOUSEKEY` 时为 `RESULT:6`）。
- `height`：显示高度，字体大小的百分比（默认 100）。负数表示纵向翻转。
- `width`：显示宽度，字体大小的百分比（默认 0 = 保持原始比例）。负数表示横向翻转。
- `ypos`：纵向偏移，字体大小的百分比（默认 0）。

`height`、`width`、`ypos` 的数值后可加 `px`（大小写不敏感）以像素为单位指定（如 `width='200px'`）。不加后缀时按字体大小的百分比解释。

### `<shape>` — 行内图形

```
<shape type='rect|space' param='参数' color='颜色' bcolor='选中色'>
```

- `type='rect'`：绘制矩形。
  - `param` 为 1 个数字时：矩形宽度（字体大小百分比）。此时等价于 `param='0,0,宽度,100'`。
  - `param` 为 4 个数字时：`x, y, 宽度, 高度`（均为字体大小百分比）。
- `type='space'`：空白占位。`param` 为宽度（字体大小百分比）。
- `color`：图形颜色。
- `bcolor`：选中时的颜色。

`param` 中的数值可以为负数（例如 `param='-100'` 表示反向间距，字符后退）。参数中的数值同样支持 `px` 后缀以像素为单位指定。

### `<clearbutton>` — 清除按钮状态

```
<clearbutton notooltip='true|false'>文字</clearbutton>
```

将包围区域的按钮状态清除（变为非按钮文字）。`title` 和 `pos` 属性功能保留。
- `notooltip='true'`：同时禁用提示文字。

### `<div>` — 子区域

```
<div width='宽' height='高' xpos='x' ypos='y'
     color='背景色' bcolor='边框色' display='relative|absolute'
     margin='边距' padding='内距' border='边框宽'
     radius='圆角'>
  内容
</div>
```

在指定区域内渲染内容。**不支持嵌套**。

| 属性 | 说明 |
|------|------|
| `width` / `height` | 子区域尺寸（支持 `px` 和百分比） |
| `xpos` / `ypos` | 偏移量。负数 = 左/上，正数 = 右/下 |
| `size` | `width,height` 的简写 |
| `rect` | `xpos,ypos,width,height` 的简写 |
| `depth` | 深度（z 序）。负数靠前（手前），正数靠后（奥） |
| `color` | 背景色 |
| `display` | `relative`（默认，跟随文字位置）或 `absolute`（窗口固定位置，`(0,0)` 为左下角，`ypos` 正方向朝上） |
| `margin` | 外边距。可 1~4 个值：`'all'` / `'左右,上下'` / `'上,左右,下'` / `'上,右,下,左'` |
| `padding` | 内边距，格式同 `margin` |
| `border` | 边框宽度，格式同 `margin` |
| `bcolor` | 边框颜色，格式同 `font` 的 `color` 属性 |
| `radius` | 圆角半径，格式同 `margin` |

## 字符引用

在 HTML_PRINT 中可用以下字符引用：

| 引用 | 字符 |
|------|------|
| `&amp;` | `&` |
| `&gt;` | `>` |
| `&lt;` | `<` |
| `&quot;` | `"` |
| `&apos;` | `'` |
| `&#nn;` | 十进制 Unicode 码点 nn |
| `&#xnn;` | 十六进制 Unicode 码点 nn |

## HTML 注释

```
<!-- 这是注释 -->
```

`<!--` 和 `-->` 之间的内容在 HTML 解析时被忽略。

## 相关函数

### HTML_ESCAPE — 转义

```
str HTML_ESCAPE(字符串)
```

将字符串中的特殊字符（`&`、`<`、`>`、`"`、`'`）转换为 HTML 字符引用。用于在 HTML_PRINT 中安全地显示可能包含特殊字符的文本。

示例：
```
PRINTFORMW %HTML_ESCAPE("A&B<C>D'E")%
; 输出：A&amp;B&lt;C&gt;D&apos;E
```

### HTML_TOPLAINTEXT — 还原纯文本

```
str HTML_TOPLAINTEXT(字符串)
```

将 HTML 格式文本中的标签和字符引用去除，还原为纯文本。与 `HTML_ESCAPE` 互为逆操作。

### 其他 HTML 相关函数

| 函数 | 说明 |
|------|------|
| `HTML_GETPRINTEDSTR` | 获取 HTML_PRINT 最近一次实际输出的字符串 |
| `HTML_POPPRINTINGSTR` | 获取并清除 HTML_PRINT 输出栈中的字符串 |
| `HTML_STRINGLEN` | 计算 HTML 标签去除后的纯文本长度 |
| `HTML_STRINGLINES` | 计算 HTML 标签去除后的纯文本行数 |
| `HTML_SUBSTRING` | 对 HTML 字符串按纯文本位置取子串 |
| `HTML_TAGSPLIT` | 将 HTML 字符串按标签和纯文本分段拆分 |

## HTML_PRINT_ISLAND — 孤岛标记注册

```
HTML_PRINT_ISLAND 标记名, HTML文本
```

将 HTML 文本注册为"孤岛标记"（island tag），后续可在 `HTML_PRINT` 中通过 `<island name='标记名' />` 引用。

- 主要用于将复杂的 HTML 片段预先定义好，在需要的地方简约引用。

```
; 定义孤岛标记
HTML_PRINT_ISLAND myHeader, "<font color='red'><b>标题文字</b></font>"

; 在 HTML_PRINT 中引用
HTML_PRINT "<island name='myHeader' />欢迎使用本程序。"
```

## 注意事项

1. `HTML_PRINT` 的标签系统是**独立**的样式体系，不受 `ALIGNMENT`、`SETCOLOR`、`SETFONT` 等全局命令影响。一旦使用 `HTML_PRINT`，所有样式都要用标签属性指定。
2. 与普通 `PRINT` 不同，`HTML_PRINT` 的参数是字符串表达式（可包含 `%变量%` 展开），行为更接近 `PRINTSL`。
3. `<div>` 标签不支持嵌套。
4. 图片、`<div>` 等内容如果高度超出所在行，即使所在行已滚出画面，这些内容仍可显示在可见区域中（不会被裁剪）。
