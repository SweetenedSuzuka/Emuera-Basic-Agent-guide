# 式中函数（内联函数）

「式中使用的函数」（略称：式中函数）是 Emuera 1.712 版本新增的语法，相当于其他编程语言的常规函数。

**注意**：本文档所述「式中函数」与其他语言的匿名函数/内联函数无关。用户自定义的式中函数（使用 `#FUNCTION`/`RETURNF`）请参阅[预处理器文档](./preprocessor.md)中「式中函数定义」部分。

## 基本用法

```
A = ABS(A)
IF STRLENS(STR:0) > A
    LOCALS:0 = %SUBSTRING(STR:0, A, 1)%
ENDIF
```

不使用式中函数的等价写法：

```
ABS A
A = RESULT
STRLENS STR:0
IF RESULT > A
    SUBSTRING STR:0, A, 1
    LOCALS:0 = %RESULTS:0%
ENDIF
```

## 函数签名约定

```
int STRLENS(str s)
str SUBSTRING(str s, int start = 0, int length = -1)
```

- 第一个词：返回值类型（`int` 或 `str`）
- 第二个词：函数名
- 括号内：参数列表（`类型 参数名`）
- `= 默认值`：参数可省略及默认值

## 返回值类型的限制

- 返回 `int` 的函数可以直接赋值给整数类型变量
- 返回 `str` 的函数**不能**直接赋值给整数类型变量

```
A = STRLENS("abc")           ; ○ STRLENS 返回 int
A = SUBSTRING("abc", 0, 1)   ; × SUBSTRING 返回 str
STR = %SUBSTRING("abc", 0, 1)% ; ○ 使用 % 嵌入
```

## 参数省略

以下写法等价：

```
STR = SUBSTRING(RESULTS)
STR = SUBSTRING(RESULTS, 0)
STR = SUBSTRING(RESULTS, , -1)
STR = SUBSTRING(RESULTS, 0, -1)
```

省略中间参数时需要保留逗号以明确位置。

## 式中函数 vs 传统命令

| 写法 | 语法 |
|------|------|
| 式中函数 | `A = ABS(X)` |
| 传统命令 | `ABS X` 然后 `A = RESULT` |

式中函数可以嵌套和链式调用，代码更简洁：

```
IF ABS(A - B) > STRLENS(STR:0)
```

## 常用内建式中函数

### 数学

| 函数 | 签名 | 说明 |
|------|------|------|
| `ABS` | `int ABS(int)` | 绝对值 |
| `MAX` | `int MAX(int a, int b)` | 最大值 |
| `MIN` | `int MIN(int a, int b)` | 最小值 |
| `SQRT` | `int SQRT(int)` | 平方根 |
| `POWER` | `int POWER(int base, int exp)` | 幂运算 |
| `RAND` | `int RAND(int min, int max)` | 随机数 |

### 字符串

| 函数 | 签名 | 说明 |
|------|------|------|
| `STRLEN` | `int STRLEN(str)` | 字节长度 |
| `STRLENS` | `int STRLENS(str)` | 字数（逻辑长度） |
| `STRLENU` | `int STRLENU(str)` | Unicode 字数 |
| `SUBSTRING` | `str SUBSTRING(str, int, int)` | 子字符串 |
| `SUBSTRINGU` | `str SUBSTRINGU(str, int, int)` | Unicode 子字符串 |
| `CHARATU` | `str CHARATU(str, int)` | Unicode 字符 |
| `STRFIND` | `int STRFIND(str, str, int)` | 查找子串位置 |
| `STRFINDU` | `int STRFINDU(str, str, int)` | Unicode 查找 |
| `STRCOUNT` | `int STRCOUNT(str, str)` | 子串出现次数 |
| `SPLIT` | `int SPLIT(str, str, ref)` | 分割字符串 |
| `TOSTR` | `str TOSTR(int, option)` | 数值转字符串 |
| `TOINT` | `int TOINT(str)` | 字符串转数值 |
| `ISNUMERIC` | `int ISNUMERIC(str)` | 是否数值 |
| `TOUPPER` | `str TOUPPER(str)` | 转大写 |
| `TOLOWER` | `str TOLOWER(str)` | 转小写 |
| `TOHALF` | `str TOHALF(str)` | 全角转半角 |
| `TOFULL` | `str TOFULL(str)` | 半角转全角 |
| `MATCH` | `int MATCH(str, str)` | 通配符匹配 |
| `REGEXPMATCH` | `int REGEXPMATCH(str, str)` | 正则匹配 |

### 数组/变量

| 函数 | 签名 | 说明 |
|------|------|------|
| `VARSIZE` | `int VARSIZE(str)` | 获取变量元素个数 |
| `ISDEFINED` | `int ISDEFINED(str)` | 变量是否已定义 |
| `EXISTVAR` | `int EXISTVAR(str)` | 变量是否存在 |
| `GETCOLOR` | `int GETCOLOR()` | 获取当前颜色 |
| `GETCONFIG` | `int GETCONFIG(str)` | 获取配置值 |
| `GETCONFIGS` | `str GETCONFIGS(str)` | 获取配置字符串值 |
| `SUMARRAY` | `int SUMARRAY(ref)` | 数组求和 |
| `MAXMINARRAY` | `int MAXMINARRAY(ref, int)` | 数组最大/最小值 |
| `INRANGEARRAY` | `int INRANGEARRAY(ref, int, int)` | 范围内元素计数 |

### 文件

| 函数 | 签名 | 说明 |
|------|------|------|
| `EXISTFILE` | `int EXISTFILE(str)` | 文件是否存在 |
| `ENUMFILES` | `int ENUMFILES(str, str, int)` | 枚举文件 |
| `EXISTCSV` | `int EXISTCSV(str)` | CSV 是否存在 |

### HTML

| 函数 | 签名 | 说明 |
|------|------|------|
| `HTML_TOPLAINTEXT` | `str HTML_TOPLAINTEXT(str)` | HTML 转纯文本 |
| `HTML_ESCAPE` | `str HTML_ESCAPE(str)` | HTML 转义 |
| `HTML_STRINGLEN` | `int HTML_STRINGLEN(str)` | HTML 字符串长度 |
| `HTML_STRINGLINES` | `int HTML_STRINGLINES(str)` | HTML 字符串行数 |
| `HTML_SUBSTRING` | `str HTML_SUBSTRING(str, int, int)` | HTML 子字符串 |
| `HTML_TAGSPLIT` | `int HTML_TAGSPLIT(str, ref)` | HTML 标签分割 |
| `HTML_GETPRINTEDSTR` | `str HTML_GETPRINTEDSTR()` | 获取已打印的 HTML |
| `HTML_POPPRINTINGSTR` | `str HTML_POPPRINTINGSTR()` | 弹出已打印的 HTML |

### 其他

| 函数 | 签名 | 说明 |
|------|------|------|
| `GETTIME` | `int GETTIME()` | 获取系统时间 |
| `GETMILLISECOND` | `int GETMILLISECOND()` | 获取毫秒 |
| `GETSECOND` | `int GETSECOND()` | 获取秒 |
| `GETMEMORYUSAGE` | `int GETMEMORYUSAGE()` | 获取内存使用量 |
| `CONVERT` | `int CONVERT(int, int, int)` | 单位换算 |
| `MONEYSTR` | `str MONEYSTR(int, option)` | 金钱格式化 |
| `BARSTR` | `str BARSTR(int, int, int)` | 进度条字符串 |
| `ERDNAME` | `str ERDNAME(int)` | 获取 ERD 文件名 |
| `CSVNAME` | `str CSVNAME(int)` | 获取 CSV 文件名 |
| `CSV_STATUS` | `int CSV_STATUS()` | CSV 加载状态 |
| `ENUMVAR` | `str ENUMVAR(int)` | 枚举变量名 |
| `ENUMFUNC` | `str ENUMFUNC(int)` | 枚举函数名 |
| `ENUMMACRO` | `str ENUMMACRO(int)` | 枚举宏名 |
| `EXISTFUNCTION` | `int EXISTFUNCTION(str)` | 函数是否存在 |
| `EXISTMETH` | `int EXISTMETH(str, str)` | 方法是否存在 |
| `GETMETH` | `str GETMETH(str, str)` | 获取方法值 |
| `GETSETVAR` | `str GETSETVAR(str, int, str)` | 获取/设置变量 |
