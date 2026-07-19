# 式中函数（内联函数）

「式中使用的函数」（略称：式中函数）是 Emuera 的表达式内置函数语法，相当于其他编程语言的常规函数。

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
- `...`：可变参数（可传入任意数量实参）

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
| `ABS` | `int ABS(int value)` | 绝对值 |
| `SIGN` | `int SIGN(int value)` | 符号：正数返回 1，零返回 0，负数返回 -1 |
| `SQRT` | `int SQRT(int value)` | 平方根（向下取整） |
| `POWER` | `int POWER(int base, int exp)` | 幂运算 base^exp |
| `CBRT` | `int CBRT(int value)` | 立方根 |
| `LOG` | `int LOG(int value)` | 自然对数 |
| `LOG10` | `int LOG10(int value)` | 常用对数（底 10） |
| `EXPOMENT` | `int EXPOMENT(int value)` | 指数函数 e^value |
| `MAX` | `int MAX(int value(, int...))` | 返回参数中的最大值（可变参数） |
| `MIN` | `int MIN(int value(, int...))` | 返回参数中的最小值（可变参数） |
| `LIMIT` | `int LIMIT(int value, int minValue, int maxValue)` | 将 value 限制在 [minValue, maxValue] 范围内 |
| `INRANGE` | `int INRANGE(int value, int minValue, int maxValue)` | value 在 [minValue, maxValue] 内返回 1，否则 0 |
| `RAND` | `int RAND(int min(, int max))` | 随机数；单参数时等同 `RAND:X`，双参数返回 [min, max) |
| `GETBIT` | `int GETBIT(int value, int bit)` | 获取 value 第 bit 位的值（0 或 1） |

### 字符串

| 函数 | 签名 | 说明 |
|------|------|------|
| `STRLEN` | `int STRLEN(str s)` | SHIFT-JIS 字节长度（全角=2） |
| `STRLENS` | `int STRLENS(str s)` | SHIFT-JIS 字节长度（全角=2） |
| `STRLENFORM` | `int STRLENFORM(str formedString)` | 格式化字符串展开后的 SHIFT-JIS 字节长度 |
| `STRLENU` | `int STRLENU(str s)` | Unicode 字数（全角=1） |
| `STRLENSU` | `int STRLENSU(str s)` | Unicode 字数（全角=1） |
| `STRLENFORMU` | `int STRLENFORMU(str formedString)` | 格式化字符串展开后的 Unicode 字数 |
| `SUBSTRING` | `str SUBSTRING(str s, int start, int count)` | 取子字符串（SHIFT-JIS 计数）；count 为负时取到末尾 |
| `SUBSTRINGU` | `str SUBSTRINGU(str s, int start, int count)` | 取子字符串（Unicode 计数）；count 为负时取到末尾 |
| `CHARATU` | `str CHARATU(str s, int position)` | 取 Unicode 位置 position 的单个字符 |
| `STRFIND` | `int STRFIND(str s, str searchWord(, int start))` | 查找 searchWord 在 s 中的位置（SHIFT-JIS 索引），未找到返回 -1 |
| `STRFINDU` | `int STRFINDU(str s, str searchWord(, int start))` | 查找 searchWord 在 s 中的位置（Unicode 索引），未找到返回 -1 |
| `STRCOUNT` | `int STRCOUNT(str s, str searchWord)` | searchWord 在 s 中出现的次数（searchWord 为正则表达式） |
| `SPLIT` | `int SPLIT(str s, str separator, strArray result)` | 以 separator 分割 s，结果写入字符串数组 result，返回分割数 |
| `STRJOIN` | `str STRJOIN(strArray array(, str separator, int start, int count))` | 将字符串数组各元素用 separator 连接；默认 separator 为逗号 |
| `REPLACE` | `str REPLACE(str s, str searchWord, str replaceWord)` | 将 s 中匹配 searchWord（正则）的部分替换为 replaceWord |
|  | `str REPLACE(str s, str searchWord, strArray array, int mode)` | mode 非 0 时，用 array 各元素顺序替换 searchWord 匹配到的各部分
| `ESCAPE` | `str ESCAPE(str s)` | 转义 s 中的正则表达式元字符 |
| `UNICODE` | `str UNICODE(int charCode)` | 返回 Unicode 码点 charCode 对应的字符 |
| `ENCODETOUNI` | `int ENCODETOUNI(str s(, int position))` | 返回 s 中 position 处字符的 Unicode 码点；position 默认 0 |
| `TOUPPER` | `str TOUPPER(str s)` | 英文字母转大写 |
| `TOLOWER` | `str TOLOWER(str s)` | 英文字母转小写 |
| `TOHALF` | `str TOHALF(str s)` | 全角字符转半角（无对应半角的字符保持原样） |
| `TOFULL` | `str TOFULL(str s)` | 半角字符转全角 |
| `TOSTR` | `str TOSTR(int value(, str format))` | 数值转字符串；format 为 C# 格式字符串（如 `"D8"`、`"X"`） |
| `TOINT` | `int TOINT(str s)` | 字符串转数值；不可解析时返回 0 |
| `ISNUMERIC` | `int ISNUMERIC(str s)` | s 可解析为数值返回 1，否则 0 |
| `STRFORM` | `str STRFORM(str formedString)` | 将 formedString 按 PRINTFORM 规则展开后返回 |
| `REGEXPMATCH` | `int REGEXPMATCH(str s, str pattern(, int output))` | 正则匹配；返回匹配数。output 非零时 RESULT:1 获取组数、RESULTS 获取各匹配结果 |
|  | `int REGEXPMATCH(str s, str pattern, ref int groupCount, ref str[] matches)` | 同上重载：groupCount 获取组数，matches 获取各匹配结果 |

### 数组 / 变量

| 函数 | 签名 | 说明 |
|------|------|------|
| `VARSIZE` | `int VARSIZE(str varName(, int dimension))` | 获取变量 varName 的数组元素个数；dimension 指定多维数组的维度（0 起始） |
| `ISDEFINED` | `int ISDEFINED(str macroName)` | 宏 macroName 已定义返回 1，否则 0 |
| `EXISTVAR` | `int EXISTVAR(str varName)` | 变量/常量 varName 已定义则返回类型位掩码，否则 0 |
| `GETVAR` | `int GETVAR(str varName)` | 以字符串 varName 获取整数型变量/常量的值 |
| `GETVARS` | `str GETVARS(str varName)` | 以字符串 varName 获取字符串型变量/常量的值 |
| `SETVAR` | `int SETVAR(str varName, any value)` | 以字符串 varName 将 value 赋值给变量；始终返回 1 |
| `VARSETEX` | `int VARSETEX(str varName, any value(, int setAllDim, int from, int to))` | 以字符串指定变量名进行 VARSET；始终返回 1 |
| `ENUMVARBEGINSWITH` | `int ENUMVARBEGINSWITH(str keyword)` | 枚举以 keyword **开头**的变量/常量名，存入 RESULTS，返回匹配数 |
| `ENUMVARENDSWITH` | `int ENUMVARENDSWITH(str keyword)` | 枚举以 keyword **结尾**的变量/常量名，存入 RESULTS，返回匹配数 |
| `ENUMVARWITH` | `int ENUMVARWITH(str keyword)` | 枚举**包含** keyword 的变量/常量名，存入 RESULTS，返回匹配数 |
| `ENUMFUNCBEGINSWITH` | `int ENUMFUNCBEGINSWITH(str keyword)` | 枚举以 keyword **开头**的函数名，存入 RESULTS，返回匹配数 |
| `ENUMFUNCENDSWITH` | `int ENUMFUNCENDSWITH(str keyword)` | 枚举以 keyword **结尾**的函数名，存入 RESULTS，返回匹配数 |
| `ENUMFUNCWITH` | `int ENUMFUNCWITH(str keyword)` | 枚举**包含** keyword 的函数名，存入 RESULTS，返回匹配数 |
| `ENUMMACROBEGINSWITH` | `int ENUMMACROBEGINSWITH(str keyword)` | 枚举以 keyword **开头**的宏名，存入 RESULTS，返回匹配数 |
| `ENUMMACROENDSWITH` | `int ENUMMACROENDSWITH(str keyword)` | 枚举以 keyword **结尾**的宏名，存入 RESULTS，返回匹配数 |
| `ENUMMACROWITH` | `int ENUMMACROWITH(str keyword)` | 枚举**包含** keyword 的宏名，存入 RESULTS，返回匹配数 |
| `SUMARRAY` | `int SUMARRAY(intArray array(, int start, int end))` | 对整数数组 [start, end) 范围内元素求和 |
| `MAXARRAY` | `int MAXARRAY(intArray array(, int start, int end))` | 返回整数数组 [start, end) 范围内的最大值 |
| `MINARRAY` | `int MINARRAY(intArray array(, int start, int end))` | 返回整数数组 [start, end) 范围内的最小值 |
| `MATCH` | `int MATCH(array arr, any value(, int start, int end))` | 统计数组 [start, end) 范围内等于 value 的元素个数 |
| `INRANGEARRAY` | `int INRANGEARRAY(intArray array, int min, int max(, int start, int end))` | 统计数组 [start, end) 范围内满足 min ≤ value < max 的元素个数 |
| `INRANGECARRAY` | `int INRANGECARRAY(charaArray array, int min, int max(, int start, int end))` | 统计角色数组 [start, end) 范围内满足 min ≤ value < max 的元素个数 |
| `SUMCARRAY` | `int SUMCARRAY(charaArray array(, int start, int end))` | 对角色数组 [start, end) 范围内元素求和 |
| `CMATCH` | `int CMATCH(charaArray array, any value(, int start, int end))` | 统计角色数组 [start, end) 范围内等于 value 的元素个数 |
| `MAXCARRAY` | `int MAXCARRAY(charaArray array(, int start, int end))` | 返回角色数组 [start, end) 范围内的最大值 |
| `MINCARRAY` | `int MINCARRAY(charaArray array(, int start, int end))` | 返回角色数组 [start, end) 范围内的最小值 |
| `GROUPMATCH` | `int GROUPMATCH(any key, any value...)` | 返回第 2 参数起等于 key 的参数个数（所有参数同类型） |
| `NOSAMES` | `int NOSAMES(any value, any value...)` | 所有参数互不相同时返回 1，否则 0 |
| `ALLSAMES` | `int ALLSAMES(any value, any value...)` | 所有参数全部相同时返回 1，否则 0 |
| `GETNUM` | `int GETNUM(variable var, str keyword)` | 返回 var 中名称等于 keyword 的元素的序号 |
| `FINDELEMENT` | `int FINDELEMENT(variable var, any value(, int start, int end, int dimension))` | 在 var 中查找 value，返回首个匹配索引；未找到返回 -1 |
| `FINDLASTELEMENT` | `int FINDLASTELEMENT(variable var, any value(, int start, int end, int dimension))` | 在 var 中反向查找 value，返回最后一个匹配索引；未找到返回 -1 |
| `GETPALAMLV` | `int GETPALAMLV(int value, int maxLV)` | 比较 value 与 PALAMLV，返回达到的最高等级 |
| `GETEXPLV` | `int GETEXPLV(int value, int maxLV)` | 比较 value 与 EXPLV，返回达到的最高等级 |
| `ERDNAME` | `str ERDNAME(variable var, int index(, int dimension))` | 获取 ERD 变量 var 中索引 index 对应的名称；dimension 指定维度（1 起始） |

### 颜色

| 函数 | 签名 | 说明 |
|------|------|------|
| `GETCOLOR` | `int GETCOLOR()` | 获取当前文字色（0xRRGGBB） |
| `GETBGCOLOR` | `int GETBGCOLOR()` | 获取当前背景色（0xRRGGBB） |
| `GETDEFCOLOR` | `int GETDEFCOLOR()` | 获取配置中默认文字色（0xRRGGBB） |
| `GETDEFBGCOLOR` | `int GETDEFBGCOLOR()` | 获取配置中默认背景色（0xRRGGBB） |
| `GETFOCUSCOLOR` | `int GETFOCUSCOLOR()` | 获取按钮选中时的文字色（0xRRGGBB） |
| `COLOR_FROMNAME` | `int COLOR_FROMNAME(str colorName)` | 颜色名转颜色值（如 `"Red"` → `0xFF0000`） |
| `COLOR_FROMRGB` | `str COLOR_FROMRGB(int r, int g, int b)` | RGB 分量转颜色名字符串 |

### 配置

| 函数 | 签名 | 说明 |
|------|------|------|
| `GETCONFIG` | `int GETCONFIG(str configWord)` | 获取配置项 configWord 的整数值 |
| `GETCONFIGS` | `str GETCONFIGS(str configWord)` | 获取配置项 configWord 的字符串值 |

### CSV

| 函数 | 签名 | 说明 |
|------|------|------|
| `CSVNAME` | `str CSVNAME(int charaNo)` | 获取 charaNo 对应 CSV 中的「名前」 |
| `CSVCALLNAME` | `str CSVCALLNAME(int charaNo)` | 获取 charaNo 对应 CSV 中的「呼び名」 |
| `CSVNICKNAME` | `str CSVNICKNAME(int charaNo)` | 获取 charaNo 对应 CSV 中的「ニックネーム」 |
| `CSVMASTERNAME` | `str CSVMASTERNAME(int charaNo)` | 获取 charaNo 对应 CSV 中的「主人称」 |
| `CSVBASE` | `int CSVBASE(int charaNo, int index)` | 获取 charaNo 的 BASE:index |
| `CSVCSTR` | `str CSVCSTR(int charaNo, int index)` | 获取 charaNo 的 CSTR:index |
| `CSVABL` | `int CSVABL(int charaNo, int index)` | 获取 charaNo 的 ABL:index |
| `CSVTALENT` | `int CSVTALENT(int charaNo, int index)` | 获取 charaNo 的 TALENT:index |
| `CSVMARK` | `int CSVMARK(int charaNo, int index)` | 获取 charaNo 的 MARK:index |
| `CSVEXP` | `int CSVEXP(int charaNo, int index)` | 获取 charaNo 的 EXP:index |
| `CSVRELATION` | `int CSVRELATION(int charaNo, int index)` | 获取 charaNo 的 RELATION:index |
| `CSVJUEL` | `int CSVJUEL(int charaNo, int index)` | 获取 charaNo 的 JUEL:index |
| `CSVEQUIP` | `int CSVEQUIP(int charaNo, int index)` | 获取 charaNo 的 EQUIP:index |
| `CSVCFLAG` | `int CSVCFLAG(int charaNo, int index)` | 获取 charaNo 的 CFLAG:index |

### 文件

| 函数 | 签名 | 说明 |
|------|------|------|
| `EXISTFILE` | `int EXISTFILE(str relativePath)` | 文件 relativePath（相对 Emuera.exe）存在返回 1，否则 0 |
| `ENUMFILES` | `int ENUMFILES(str dir(, str pattern, int option))` | 枚举 dir 下匹配 pattern 的文件名存入 RESULTS，返回匹配数；option 非零时搜索子文件夹 |
| `EXISTCSV` | `int EXISTCSV(int charaNo)` | charaNo 对应的 CSV 已定义返回 1，否则 0 |

### HTML

| 函数 | 签名 | 说明 |
|------|------|------|
| `HTML_TOPLAINTEXT` | `str HTML_TOPLAINTEXT(str html)` | HTML 转纯文本（去除标签、展开字符引用） |
| `HTML_ESCAPE` | `str HTML_ESCAPE(str html)` | 将字符串转义为 HTML 字符引用 |
| `HTML_STRINGLEN` | `int HTML_STRINGLEN(str html(, int returnPixel))` | HTML 字符串显示宽度；returnPixel 非零时返回像素数，否则返回半角字符数 |
| `HTML_STRINGLINES` | `int HTML_STRINGLINES(str html, int width)` | HTML 字符串按 width（半角字符数）折行后的行数 |
| `HTML_SUBSTRING` | `str HTML_SUBSTRING(str html, int width)` | HTML 字符串按 width（半角字符数）截取第一行，剩余内容写入 RESULTS:1 |
| `HTML_GETPRINTEDSTR` | `str HTML_GETPRINTEDSTR(int lineNo)` | 获取已显示的第 lineNo 行 HTML 内容 |
| `HTML_POPPRINTINGSTR` | `str HTML_POPPRINTINGSTR()` | 获取当前打印缓冲区中的 HTML 内容并清空缓冲区 |

### 其他

| 函数 | 签名 | 说明 |
|------|------|------|
| `CONVERT` | `str CONVERT(int value, int base)` | 将 value 按 base 进制转为字符串；base 仅支持 2、8、10、16 |
| `MONEYSTR` | `str MONEYSTR(int value(, str format))` | 数值格式化为金钱字符串（自动附加 replace.csv 中的单位）；format 同 TOSTR |
| `BARSTR` | `str BARSTR(int value, int maxValue, int length)` | 生成与 BAR 命令同样式的进度条字符串 |
| `GETTIME` | `int GETTIME()` | 获取当前系统时间的整数值（YYYYMMDDHHMMSSmmm） |
| `GETTIMES` | `str GETTIMES()` | 获取当前系统时间的字符串（`YYYY/MM/DD HH:MM:SS`） |
| `GETMILLISECOND` | `int GETMILLISECOND()` | 获取自 0001-01-01 以来的毫秒数 |
| `GETSECOND` | `int GETSECOND()` | 获取自 0001-01-01 以来的秒数 |
| `GETMEMORYUSAGE` | `int GETMEMORYUSAGE()` | 获取当前 Emuera 内存使用量（字节） |
| `EXISTFUNCTION` | `int EXISTFUNCTION(str funcName)` | 函数 funcName 存在则返回类型码（1=普通函数，2=数值型式中函数，3=字符串型式中函数），不存在返回 0 |
| `EXISTMETH` | `int EXISTMETH(str functionName)` | 自定义式中函数 functionName 存在则返回类型码（1=#FUNCTION，2=#FUNCTIONS），不存在返回 0 |
| `GETMETH` | `int GETMETH(str functionName(, int defaultValue, any argument...))` | 以字符串调用数值型式中函数；defaultValue 为函数不存在时的默认返回值 |
| `GETMETHS` | `str GETMETHS(str functionName(, str defaultValue, any argument...))` | 以字符串调用字符串型式中函数；defaultValue 为函数不存在时的默认返回值 |

## 动态函数调用（GETMETH / GETMETHS）

`GETMETH` 和 `GETMETHS` 允许通过**字符串名称**在运行时动态调用自定义式中函数。这是实现回调、插件系统等高级模式的关键机制。

```
; 定义式中函数
@计算奖励(基础值, 倍率)
#FUNCTION
    RETURNF 基础值 * 倍率 / 100

@获取称号(等级)
#FUNCTIONS
    SELECTCASE 等级
    CASE 0
        RETURNF "新手"
    CASE 1
        RETURNF "高手"
    CASEELSE
        RETURNF "大师"
    ENDSELECT

; 动态调用
VARS 函数名 = "计算奖励"
A = GETMETH(函数名, 0, 1000, 150)
; A = 1000 * 150 / 100 = 1500

VARS 函数名 = "获取称号"
STR:0 = GETMETHS(函数名, "未知", 2)
; STR:0 = "大师"

; 函数不存在时使用默认值（不会报错）
A = GETMETH("不存在的函数", -1, 任意参数)
; A = -1（返回默认值）
```

- 第 1 参数是函数名字符串
- 第 2 参数是函数不存在时的默认返回值
- 第 3 参数起是传递给目标函数的实参
- 总参数数量 = 目标函数参数数 + 2（函数名 + 默认值）

## 短路求值与式中函数

逻辑运算符 `&&` 和 `||` 采用短路求值（短絡評価）：

- `A && B`：如果 A 为假（0），B 不会被求值
- `A || B`：如果 A 为真（非0），B 不会被求值

这意味着如果式中函数出现在短路求值跳过的部分，它不会被执行：

```
A = 0
X = (A != 0) && DOUBLE(A)   ; DOUBLE 不会被调用
```

**编写式中函数时必须注意**：

- 不要把副作用逻辑（修改全局变量、输出等）放入式中函数
- 式中函数应只做计算并返回结果
- 因为短路求值可能导致函数不执行，依赖式中函数的副作用的代码是不可靠的

## 伪变量与式中函数的区别

以下看起来像式中函数，但实际上是**伪变量**——它们每次求值时重新计算，但不接受参数：

- `RAND`：随机数，每次求值时重新生成
- `COUNT`：REPEAT 循环的计数器
- `CHARANUM`：当前角色数

这意味着在同一个表达式里，`RAND(100) + RAND(100)` 中的两个 `RAND` 会独立生成随机数——它们不是被"调用"两次，而是各自独立求值。

因此 `RAND` 有两种书写形式：

```
A = RAND(100)           ; 0～99
A = RAND(1, 101)        ; 1～100
```

使用 `RANDOMIZE` 命令初始化随机数种子。
