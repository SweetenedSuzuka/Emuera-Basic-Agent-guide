# 版本差异

本文档将说明各版本间的差异。涉及三个版本：
- **eramaker**：最初的ERA解释器
- **Emuera**：Emuera 原版，第二代解释器
- **当前版本**：Emuera.NET 1824+v24+EMv18+EEv55，是最新版

Emuera 原版后来延伸出了.NET版、EM版、EE版三种，当前版本顾名思义，是以 .NET 版本为基础，结合原版v24、EMv18、EEv55得来的版本，可以视为 Emuera 这个引擎的最终版本。

> 现在的游戏分发通常直接附带脚本解释器，无需考虑跨版本兼容。本文档仅供维护旧项目或阅读旧代码时参考。

---

## 注释

| 注释前缀 | eramaker | Emuera | 当前版本 |
|---------|----------|--------|-------|
| `;` | 整行注释 | 整行注释 | 整行注释 |
| `;!;` | 整行注释 | **有效代码** | **有效代码** |
| `;^;` | 整行注释 | 整行注释 | **有效代码** |
| `;#;` | 整行注释 | 仅调试模式有效 | 仅调试模式有效 |

> **不建议**使用除了 `;` 外的注释前缀。它们在开发中大多没有意义——游戏分发直接附带解释器，无需用注释前缀区分版本。

### 行尾注释

```
A = B ;把B赋值给A
```

eramaker 不支持行尾注释，Emuera 及以后的版本支持。但无论什么版本， `PRINT` 等命令的参数字符串中 `;` 不会被解析为注释。

### 与 `[SKIPSTART]`/`[SKIPEND]` 的联用

```
;!;[SKIPSTART]
PRINTW 此脚本无法在Emuera以外运行
QUIT
;!;[SKIPEND]
```

Emuera 遇到 `;!;` 开头的行时会去掉前缀再解析，因此上述代码在 Emuera 中等效于被 `[SKIPSTART]`～`[SKIPEND]` 包围（被跳过）。在 eramaker 中 `;!;` 是普通注释，三行中间的代码正常执行。
`;^;` 在最新版本中同理。

---

## 控制流

### SIF 后的空行/注释行

```
SIF 条件语句
    ;注释
    PRINT 示例文本
```

- eramaker：始终执行 PRINT（认为 `;注释` 是 SIF 的下一行）
- Emuera / 当前版本：仅在条件成立时执行（忽略空行和注释行）

eramaker 中 SIF 后可接 IF、REPEAT 等，Emuera 对此有限制。

### IF/ELSEIF 参数省略

- eramaker：动作不定
- Emuera / 当前版本：省略参数视为 `0`（条件不成立），发出警告

### REPEAT-REND 结束时 COUNT 加算

eramaker 中 `REPEAT-REND` 退出时 `COUNT` 会 +1（`BREAK` 退出时也会）。Emuera 保持此行为。这与一般语言的 `for` 不同，注意。

### CALL 中 JUMP

eramaker 中从 `CALL` 调用的函数内不能 `JUMP`。Emuera 可以：`JUMP` 后的 `RETURN` 等同于在 `JUMP` 元函数中 `RETURN`。

---

## 变量

### 整数范围

| 引擎 | 范围 |
|------|------|
| eramaker | 32 位（-2147483648 ～ 2147483647） |
| Emuera / 当前版本 | 64 位（-9223372036854775808 ～ 9223372036854775807） |

### NAME / CALLNAME 赋值

- eramaker：不可赋值
- Emuera / 当前版本：可赋值

### RAND / CHARANUM 赋值

- eramaker：可赋值（但无意义）
- Emuera / 当前版本：禁止赋值

### CALLNAME 为空时的回退

- eramaker：`CALLNAME` 为空时自动返回 `NAME` 的值
- Emuera / 当前版本：`CALLNAME` 为空则返回空字符串。可通过配置项「CALLNAMEが空文字列の時にNAMEを代入する」恢复 eramaker 行为

### RAND 的随机范围

- eramaker：`RAND:X` 中 X=0 返回 0；否则返回 `(0～32767 随机数) % |X|`，范围受限且 X 大时偏倚明显
- Emuera / 当前版本：`RAND:X` 中 X 必须为正，返回 `(0～2^64 随机数) % X`，X 可达 `9223372036854775807`

---

## 函数与事件

### 函数名可用字符

- eramaker：符号和全角字符均可
- Emuera / 当前版本：只允许全角字符和 `_`，不允许 `@`、`,`、`(`、`{`、`}`、`%` 等符号。函数名不能以半角数字开头（否则无法作为式中函数调用）

### EVENT 函数的 #PRI / #LATER / #SINGLE

eramaker 中事件函数按 `#PRI` → 无属性 → `#LATER` 分组依次调用，每组内 `#SINGLE` 返回 1 时中断该组。

Emuera 1.800 起精确再现此行为。更早的 Emuera 版本将三者统一排序且 `#SINGLE` 会中断全部。

### NEXTCOM

eramaker 中 `NEXTCOM` 初始值 -1，执行后变为 0（而非 -1），因此不重新赋值会反复执行 `COM0`。

Emuera 保持此行为但**不推荐使用** `NEXTCOM`。若不需要 eramaker 兼容，用 `DOTRAIN` 或 `CALLTRAIN`。

---

## 字符串与输出

### PRINTFORM 展开

- eramaker：反复展开直到没有可展开项（可能因循环引用而冻结）
- Emuera / 当前版本：仅展开一次

### WAIT 换行行为

- eramaker：`WAIT` 执行时不换行，Enter 后换行
- Emuera / 当前版本：`WAIT` 执行时若光标在行中则先换行；Enter/点击后不换行

### 宏

eramaker 不支持宏。Emuera 支持（`\e`、`\n`、`(～～)*n`）。

---

## 运算符

Emuera 相对 eramaker 新增：

- `<<`、`>>` 位移
- `~` 位取反
- `^` 位异或
- `^^` 逻辑异或
- `!&`、`!|` 逻辑与非/或非
- `?～#` 三目运算符
- `++`、`--` 自增/自减

---

## 字符串操作

Emuera 相对 eramaker 新增：

- 数组元素的字符串指定：`CFLAG:"名字":0`
- 数组批量赋值：`A:10 = 1,2,3`
- FORM 语法赋值
- 字符串表达式赋值（`'=` 运算符）
- 字符串比较运算符：`==`、`!=`、`<`、`>`、`<=`、`>=`

---

## 数据与存档

### 字符编码

- eramaker：仅支持 Shift_JIS
- Emuera / 当前版本：默认 UTF-8，也可以用 Shift_JIS

### 换行操作

- eramaker：仅 `[CR][LF]` 和 `[LF]` 为换行，`[CR]` 单独不视为换行（会导致误动作）
- Emuera / 当前版本：`[CR]` 也视为换行

### 角色上限

| 引擎 | 上限 |
|------|------|
| eramaker | 约 100 人（chara 编号重叠） |
| Emuera / 当前版本 | 内存允许的任意数量 |

### 存档压缩

当前版本 支持存档压缩（需在 `emuera.config` 中启用「セーブデータを圧縮して保存する」，且需同时启用「セーブデータをバイナリ形式で保存する」）。

### XML / MAP / DataTable 的存档持久化

当前版本 支持通过 `CSV/VarExt*.csv` 配置 XML、MAP、DataTable 随存档保存。详见 `references/commands/data-save-load.md`。

---

## CSV 与资源

### CSV 中的异常数值

eramaker 中 `0xFF` 被当作 `0` 处理。Emuera 报错并禁用该定义。

### abl.csv 等的索引范围

- eramaker：可指定负数或超大索引（但实际使用时会因引用越界出错）
- Emuera / 当前版本：只接受数组范围内的索引，超范围的行被忽略。可通过 `VariableSize.csv` 调整数组大小

### train.csv 的索引范围

- eramaker：可指定任意索引，只要对应 `@COM` 函数存在即可执行；负值显示但不执行
- Emuera / 当前版本：只接受 `TRAINNAME` 数组范围内的索引（默认 0～999）

### chara*.csv 的基础/素质值

eramaker 中 `基础,0`（缺第三值）视为 1，`素質,0,100`（多余第三值）忽略第三值视为 1。Emuera 如实读取：`基础,0` 则 `MAXBASE:0=1`，`素質,0,100` 则 `TALENT:0=100`。

### 文件读入顺序

eramaker 和 Emuera 均依赖文件系统决定文件读入顺序，在 FAT 文件系统上可能不正常工作。大多数现代脚本假定 NTFS。

### ERD 文件（变量数组元素命名）

当前版本 支持通过 `.ERD` 文件为 `#DIM`/`#DIMS` 定义的数组变量提供字符串名称映射。详见 `references/core-concepts/user-defined-variables.md`。

### Alias 别名（.als）

当前版本 支持通过 `.als` 文件为 CSV 变量提供额外别名。详见 `references/csv-reference/csv-format.md`。

### WebP 图像格式

当前版本 支持 WebP 图像格式。

### font/ 动态加载

当前版本 支持从 `font/` 文件夹动态加载 `.ttf`/`.otf` 字体文件，无需安装到系统。

---

## 音频

### 音频文件支持

当前版本 支持通过 `sound/` 文件夹播放音频（PLAYSOUND、PLAYBGM 等）。详见 `references/commands/sound.md`。

### 音频文件锁定

当前版本 运行时音频文件不会被锁定（允许外部替换）。

---

## 调试

### 调试命令

Emuera 支持 `;#;` 调试行、`[IF_DEBUG]`/`[IF_NDEBUG]` 条件块、`DEBUGPRINT` 等调试功能。详见 `references/reference/debug.md`。

### 旧式 @ 调试命令

eramaker 和 Emuera 均支持以 `@` 前缀在控制台输入调试命令（如 `@MONEY=10000`），但执行后 MASTER 名会变为「イカサマ」。Emuera 需在配置中启用「デバッグコマンドを使用する」。

---

## SP 角色

eramaker 中 `CFLAG:0` 非 0 的角色被标记为 SP 角色，只能通过 `ADDSPCHARA` 登记。

Emuera 1.816 起废弃此功能——`CFLAG:0` 不再有特殊含义，所有角色通过 `ADDCHARA` 登记。可通过兼容选项「SPキャラを使用する」恢复 eramaker 行为，但不推荐。