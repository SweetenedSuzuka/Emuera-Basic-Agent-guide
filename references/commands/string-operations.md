# 字符串操作命令

## 大小写/全半角变换

| 命令 | 参数 | 返回 | 说明 |
|------|------|:--|------|
| `TOUPPER` | `string` | `string` | 转大写 |
| `TOLOWER` | `string` | `string` | 转小写 |
| `TOHALF` | `string` | `string` | 全角→半角 |
| `TOFULL` | `string` | `string` | 半角→全角 |

## 类型变换

| 命令 | 参数 | 返回 | 说明 |
|------|------|:--|------|
| `TOSTR` | `int` [, `option`] | `string` | 数值→字符串 |
| `TOINT` | `string` | `int` | 字符串→数值 |
| `ISNUMERIC` | `string` | `int` | 是否数值（1=是/0=否） |

```
A = TOINT("123")         ; A = 123
STR:0 = TOSTR(MONEY)     ; STR:0 = "1000" (if MONEY=1000)
IF ISNUMERIC(STR:0)      ; 检查 STR:0 是否为数值
```

## 字符串长度

| 命令 | 参数 | 返回 | 说明 |
|------|------|:--|------|
| `STRLEN` | `string` | `int` | 字节长度 |
| `STRLENS` | `string` | `int` | 文字数（逻辑长度） |
| `STRLENU` | `string` | `int` | Unicode 文字数 |
| `STRLENSU` | `string` | `int` | Unicode 文字数（逻辑） |
| `STRLENFORM` | `string` | `int` | FORMAT 后长度 |
| `STRLENFORMU` | `string` | `int` | FORMAT 后 Unicode 长度 |

## 子字符串

| 命令 | 参数 | 返回 | 说明 |
|------|------|:--|------|
| `SUBSTRING` | `str`, `int start`, `int length` | `string` | 子字符串 |
| `SUBSTRINGU` | `str`, `int start`, `int length` | `string` | Unicode 子字符串 |
| `CHARATU` | `str`, `int index` | `string` | Unicode 第 n 字符 |

```
STR:1 = SUBSTRING(STR:0, 0, 3)    ; 前 3 字节
STR:2 = SUBSTRINGU(STR:0, 0, 2)   ; 前 2 个 Unicode 字符
STR:3 = CHARATU(STR:0, 0)         ; 第 1 个字符
```

## 查找

| 命令 | 参数 | 返回 | 说明 |
|------|------|:--|------|
| `STRFIND` | `str haystack`, `str needle` [, `int start`] | `int` | 查找子串（未找到=-1） |
| `STRFINDU` | `str haystack`, `str needle` [, `int start`] | `int` | Unicode 查找 |
| `STRCOUNT` | `str`, `str` | `int` | 子串出现次数 |

```
pos = STRFIND(STR:0, "abc")       ; 查找 "abc"
count = STRCOUNT(STR:0, "a")      ; 统计 "a" 出现次数
```

## 字符串分割

| 命令 | 参数 | 返回 | 说明 |
|------|------|:--|------|
| `SPLIT` | `str source`, `str delimiter`, `ref string[]` | `int` | 分割字符串，返回分割数 |

```
; 分割 STR:0 到 ARRAY
SPLIT STR:0, ",", ARRAY
; ARRAY:0, ARRAY:1, ... 存入各段
```

## 匹配

| 命令 | 参数 | 返回 | 说明 |
|------|------|:--|------|
| `MATCH` | `str`, `str pattern` | `int` | 通配符匹配 |
| `REGEXPMATCH` | `str`, `str regex` | `int` | 正则匹配 |

```
IF MATCH(STR:0, "abc*")       ; 通配符
IF REGEXPMATCH(STR:0, "^[0-9]+$")  ; 正则：是否纯数字
```

## 替换与连接

### REPLACE — 正则替换

```
str REPLACE(源字符串, 匹配模式, 替换文字)
```

使用正则表达式在源字符串中查找匹配模式，将所有匹配部分替换为替换文字。命令形式和式中函数形式均支持。

- 第 2 参数使用 C# 正则表达式语法，因此 `()`、`[]`、`$`、`.`、`*`、`+` 等正则元字符需用 `\` 转义
- 如需匹配这些字符的字面量，先用 `ESCAPE` 对模式字符串转义

```
; 去除所有非数字字符
STR:1 = REPLACE("12亿3456万7890円", "[^0-9]", "")
; STR:1 = "1234567890"

; 替换特定词语
STR:1 = REPLACE(STR:0, "苹果", "橘子")
```

### ESCAPE — 正则元字符转义

```
str ESCAPE(字符串)
```

将字符串中的正则表达式元字符（`.$^{[(|)*+?\`）前加上 `\` 进行转义。用于准备 `REPLACE` 或 `REGEXPMATCH` 中的字面量匹配模式。

```
; 安全地把用户输入转为正则字面量
VARS 用户输入 = "1+1=2?"
VARS 安全模式 = ESCAPE(用户输入)
; 安全模式 = "1\+1=2\?"
; 现在可以在 REPLACE 中安全使用
```

### STRJOIN — 字符串数组连接

```
str STRJOIN(字符串数组 [, 分隔符, 起始索引, 数量])
```

将字符串数组的各元素用分隔符拼接为一个字符串。

| 参数 | 说明 |
|------|------|
| `字符串数组` | 源数组（如 `STR`、`LOCALS` 或用户定义的 `#DIMS` 数组） |
| `分隔符` | 元素之间的分隔字符串。省略时默认为 `,`（逗号） |
| `起始索引` | 起始元素位置（包含），省略为 `0` |
| `数量` | 要拼接的元素个数，省略时拼接到末尾 |

```
; 将 STR:0~STR:4 用逗号连接
STR:10 = STRJOIN(STR, ",", 0, 5)

; 用换行连接所有 LOCALS
RESULTS = STRJOIN(LOCALS, "\n")
```

## 格式化字符串展开

### STRFORM — 运行时展开格式化字符串

```
str STRFORM(格式化字符串)
```

将给定的字符串按 `PRINTFORM` 规则展开后返回。即 `{变量}` 替换为变量值、`%变量%` 替换为字符串变量值。命令形式和式中函数形式均支持。

```
; 动态构造并展开格式化字符串
VARS 模板 = "今天是第{DAY}天，金钱为{MONEY}"
RESULTS = STRFORM(模板)
; RESULTS = "今天是第5天，金钱为1000"（取决于实际值）
```

## 转义与 UNICODE

### UNICODE — 码点与字符互转

```
str UNICODE(int 码点)      ; 返回 Unicode 码点对应的字符
int UNICODE(str 字符)      ; 返回字符的 Unicode 码点（仅首个字符）
```

```
; 码点 → 字符
STR:0 = UNICODE(65)         ; STR:0 = "A"

; 字符 → 码点
A = UNICODE("A")            ; A = 65
```

### ENCODETOUNI — 获取指定位置字符的码点

```
int ENCODETOUNI(字符串 [, 位置])
```

返回字符串中指定位置的字符的 Unicode 码点。`位置` 以 Unicode 字符为单位，默认为 `0`（第一个字符）。

```
; 获取第三个字符的码点
A = ENCODETOUNI("ABC", 2)   ; A = 67 ('C')
```
