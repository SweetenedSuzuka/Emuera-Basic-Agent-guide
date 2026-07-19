# 与 eramaker 的差异

本文档说明 Emuera 与 eramaker 之间的关键差异。

## 修复的 Bug

### 数组最后元素不可使用
eramaker 中数组最后元素非 0 时，存档数据会被**破坏**。
Emuera 已修复此问题。
注意：Emuera 存档在 eramaker 中加载时问题会重现。

### 单目运算符 `-` 的异常
eramaker 中 `-100 < 0` 为 false 等问题。
Emuera 中已修复。

### 文件最后行被忽略
eramaker 忽略没有换行符的最后一行（CSV 和 ERB 都受影响）。
Emuera 不再现此行为。

### 数组多余元素被忽略
eramaker 中 `A:1:2 = 34` 会被执行为 `A:1 = 34`（将错误的二维访问降为一维）。
Emuera 报错。

### 数组访问特定格式不可用
eramaker 中二维数组不支持 `ABL:0:2` 或 `TALENT:(COUNT+1):2` 格式。
Emuera 支持任何格式的二维数组访问。

### CSV 中异常数值被当作整数处理
eramaker 中 `0xFF` 被当作 `0` 处理。
Emuera 报错并禁用该定义。

### 不自然表示法可执行
eramaker 中 `A:0:1:99999 +-RESULTS:0=@=+123|*?=Y` 等可以执行。
Emuera 报错。

## 其他差异

### SIF 后的空行/注释行

```
SIF 条件语句
    ;注释
    PRINT 示例文本
```

- eramaker：始终执行 PRINT（认为 `;注释` 是 SIF 的下一行）
- Emuera：仅在条件成立时执行（忽略空行和注释行）

eramaker 中 SIF 后可接 IF、REPEAT 等，Emuera 对此有限制。

### IF/ELSEIF 参数省略

- eramaker：动作不定
- Emuera：省略参数视为 `0`（条件不成立），发出警告

### 函数名可用字符

eramaker 中符号和全角字符均可用于函数名。
Emuera 中只能使用全角字符和 `_`，不能使用 `@`、`,`、`(` 等符号。
函数名不允许以半角数字开头。

### 整数范围

| 引擎 | 范围 |
|------|------|
| eramaker | 32 位（-2147483648 ～ 2147483647） |
| Emuera | 64 位（-9223372036854775808 ～ 9223372036854775807） |

### 字符编码

eramaker 使用 Shift_JIS。
Emuera 使用 Unicode（UTF-8）。

### 角色上限

| 引擎 | 上限 |
|------|------|
| eramaker | 约 100 人（chara 编号重叠） |
| Emuera | 内存允许的任意数量 |

### 变量差异

| 变量 | eramaker | Emuera |
|------|----------|--------|
| `NAME`、`CALLNAME` | 不可赋值 | 可赋值 |
| `RAND`、`CHARANUM` | 可赋值（但无意义） | 禁止赋值 |

### 宏

Emuera 支持宏功能（`\e`、`\n`、`(～～)*n`）。

### 注释

- 行尾注释（`;` 后的内容）
- `;^;` 开头的行（Emuera EM+EE 中有效，Emuera 和 eramaker 中被当作注释）
- `;!;` 开头的行（Emuera 中有效，eramaker 中被当作注释）
- `;#;` 开头的行（调试模式专用，详见 `references/reference/debug.md`）

> **不建议**使用除了 `;` 外的注释前缀，它们在开发中大多没有意义，现在的游戏分发通常直接附带脚本解释器，无需考虑兼容其它解释器的问题。

#### 与 `[SKIPSTART]` 和 `[SKIPEND]` 的联用

通过与 `[SKIPSTART]` 和 `[SKIPEND]` 的联用，可以实现一些代码只在 Emuera 中执行或只在 eramaker 中执行。

##### 只在 Emuera 中执行（使用 `!;` 前缀联用）

```
;!;[SKIPSTART]
PRINTW 此脚本无法在Emuera以外运行  
QUIT  
;!;[SKIPEND]
```

虽然 `;` 是注释标志，但是 `;!;` 在 Emuera 有特殊处理。
Emuera 遇到 `;!;` 开头的行时，会去掉 `;!;` 前缀后再解析剩余内容。

上述写法在 Emuera 中等效于：

```
[SKIPSTART]
PRINTW 此脚本无法在Emuera以外运行  
QUIT  
[SKIPEND]
```

因此会被跳过。

而在 eramaker 中等效于：

```
PRINTW 此脚本无法在Emuera以外运行  
QUIT  
```

即前后的 `;!;` 被当作了注释，因此里面的代码被执行了。

### 字符串操作

Emuera 新增：
- 数组元素的字符串指定：`CFLAG:"名字":0`
- 数组批量赋值：`A:10 = 1,2,3`
- FORM 语法赋值
- 字符串表达式赋值（`'=` 运算符）
- 字符串比较运算符：`==`、`!=`、`<`、`>`、`<=`、`>=`

### 运算符

Emuera 新增：
- `<<`、`>>` 位移
- `~` 位取反
- `^` 位异或
- `^^` 逻辑异或
- `!&`、`!|` 逻辑与非/或非
- `?～#` 三目运算符
- `++`、`--` 自增/自减
