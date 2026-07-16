# 表达式与语法扩展

本文档说明 Emuera 在 eramaker 基础上扩展的语法特性。

## 行尾注释

```
A = B ;把B赋值给A
```

行尾可以追加注释。但 PRINT 等命令的参数字符串中的 `;` 不会被解析为注释：

```
PRINT foobar;示例文本   ; 打印 "foobar;示例文本"
```

## 行连接

用 `{` 和 `}` 包围的多行内容被视为一行：

```
{
    #DIM CONST HOGE =
        1,2,3,4
}
; 等价于: #DIM CONST HOGE = 1,2,3,4
```

注意：
- `{` 和 `}` 所在行不能有其他字符（空白除外）
- 换行处会补入半角空格
- 行连接在注释解析之前处理

## 特殊注释行

### `;!;` — Emuera 专用有效行

以 `;!;` 开头的行在 Emuera 中视为有效代码，在 eramaker 中视为注释：

```
;!;PRINTW 此脚本无法在Emuera中执行
;!;QUIT
```

与 `[SKIPSTART]`/`[SKIPEND]` 配合可以实现 Emuera 独占代码：

```
;!;[SKIPSTART]
PRINTW 此脚本无法在Emuera以外的引擎中执行
QUIT
;!;[SKIPEND]
```

### `;#;` — 调试模式专用行

以 `;#;` 开头的行仅在调试模式下执行：

```
;#;PRINTL DEBUG: 当前MONEY={MONEY}
```

非调试模式下视为注释，不执行。DEBUG 系命令本身在非调试模式下也会被忽略。

## 角色数组增强

eramaker 最多 100 个角色，Emuera 不限制数量（仅受内存限制）。

`chara*.csv` 匹配所有符合格式的文件，如 `chara101.csv`、`charaABC.csv` 等。

番号重叠时，先读取的优先。

## 整数范围

eramaker：32 位（`-2147483648 ～ 2147483647`）

Emuera：64 位（`-9223372036854775808 ～ 9223372036854775807`）

## 数组变量的批量赋值

```
; 向 A:10～A:12 赋值 1,2,3
A:10 = 1,2,3

; 向 DA:0:0～DA:0:2 赋值 1,2,3
DA:0:0 = 1,2,3
```

- 不可用于复合赋值（`A += 1,2,3` 等不可）
- 字符串数组需要使用字符串表达式：

```
; 将 "草莓,甜瓜,蓝色夏威夷" 赋值给 STR:20
STR:20 = 草莓,甜瓜,蓝色夏威夷

; 分别赋值给 STR:20～STR:22
STR:20 '= "草莓", "甜瓜", "蓝色夏威夷"
```

## FORM 语法（格式化字符串）

字符串变量赋值时可以使用与 `PRINTFORM` 相同的格式：

```
SAVESTR:0 = %RESULTS%
```

使用 `\` 转义：

```
SAVESTR:0 = \%RESULT\%       ; 赋值 "%RESULT%" 字面量
SAVESTR:0 = \\               ; 赋值 "\"
```

## 字符串表达式赋值

使用 `'=` 运算符和字符串表达式：

```
STR '= "字符串"                         ; STR = 字符串
STR '= TSTR:0 + "示例"               ; STR = %TSTR:0%示例
```

## 字符串形式的数组索引

以下变量支持用 CSV 定义的字符串名引用：

- `ITEM`（item.csv）
- `TALENT`（talent.csv）
- `ABL`（abl.csv）
- `BASE`（base.csv）
- `EXP`（exp.csv）
- `MARK`（mark.csv）
- `PALAM`（palam.csv）
- `EX`（ex.csv）
- `STAIN`（stain.csv）
- `SOURCE`（source.csv）
- `CFLAG`（cflag.csv）
- `FLAG`（flag.csv）
- `TFLAG`（tflag.csv）
- `STR`（strname.csv）
- `TSTR`（tstr.csv）
- `CSTR`（cstr.csv）
- `EQUIP`（equip.csv）
- `TEQUIP`（tequip.csv）

```
; 数值索引
ABL:MASTER:0 = 10

; 字符串索引
ABL:MASTER:"体力" = 10
```

## 三目运算符

### 数值型

```
X = condition ? value_if_true # value_if_false
```

等价于：

```
IF condition
    X = value_if_true
ELSE
    X = value_if_false
ENDIF
```

可嵌套在表达式中：`Y = (X > 0 ? X # -X) * 2`

### 字符串型

```
STR '= \@ condition ? "true_string" # "false_string" \@
```

字符串型三目运算中 `#` 不可省略。

## 自增 / 自减

```
A++     ; A = A + 1
A--     ; A = A - 1
```

不能与其他运算符组合使用。
