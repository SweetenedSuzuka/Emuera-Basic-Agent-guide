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

## ESCAPE（转义）

```
ESCAPE string
```

对字符串进行转义处理。

## UNICODE 相关

```
UNICODE int     ; 返回 Unicode 码位对应的字符
UNICODE string  ; 返回字符的 Unicode 码位
```
