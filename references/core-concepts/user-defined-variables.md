# 用户定义变量

`#DIM`（整数类型）和 `#DIMS`（字符串类型）用于定义自定义变量。

## 定义位置与作用域

ERA 没有类，所有函数全局平级。变量只有两种：

| 定义位置 | 作用域 | 约束 |
|----------|--------|------|
| ERH 文件内 | 全局变量，所有函数均可访问 | 可以写 `#DIM` / `#DIMS` 和 `#DEFINE` |
| 函数声明的紧邻下一行 | 局部变量，仅在该函数内有效 | 一行一个 `#DIM` / `#DIMS`，必须写在 `@` 函数定义的下一行 |
| 函数体内任意位置 | 局部变量，仅在该函数内有效 | 使用 `VARI` / `VARS` 命令，无需写在函数头部（见下方「函数体内动态定义」） |

> **不能**在 ERB 文件顶部或函数之间写 `#DIM(S)`。那些行不属于任何函数，会变成孤立行直接报错。ERA 没有文件级变量。  
> 原理详见`references\csv-reference\erb-format.md`

## 基本书写格式

```
; 私有变量（函数内）
@FUNC_NAME
#DIM MY_INT, 100        ; 整数类型一维数组，100 元素
#DIMS MY_STR, 50        ; 字符串类型一维数组，50 元素
#DIM MY_2D, 10, 20      ; 整数类型二维数组
#DIM MY_3D, 5, 10, 15   ; 整数类型三维数组（最多三维）

; 全局变量（ERH 内）
#DIM GLOBAL_INT, 100
#DIMS GLOBAL_STR, 50
```

命名规则：
- 首字符不能是数字
- 符号只能用 `_`
- 不能与已有命令名重复（如 `PRINTFORM`、`CALL` 等不可）
- 元素个数范围：`1 ～ 1000000`
- 省略元素个数时默认为 `1`

## 初始值设定

仅一维数组支持初始值：

```
; 元素个数自动为 3
#DIM HOGE = 1,2,3

; 元素个数指定为 100，前三个元素初始化
#DIM PUGE,100 = 4,5,6

; 字符串类型
#DIMS SHOGE = "A", "B", "C"

; 元素个数大于初始值数：非 CONST 时允许（剩余元素填 0）
; #DIM PUGE,100 = 4,5,6         ← 允许（97个元素填0）
; 但 CONST 要求元素数与初始值数完全一致
; #DIM CONST PUGE,100 = 4,5,6   ← 错误（100 ≠ 3）
```

## 静态变量

```
#DIM 数字变量, 100
#DIMS 字符串变量, 50
```

`#DIM(S)` 后面什么都不加的就是静态变量。
函数退出之后，静态变量的值不会被重置，因此如果使用静态变量，需要这一点。
推荐的方法是把那些需要重置的变量统一在函数开头进行初始化。

## 动态变量（DYNAMIC）

```
#DIM DYNAMIC DYN_VAR, 100
#DIMS DYNAMIC DYN_STR, 50
```

- 每次函数调用时分配，函数结束时释放
- 递归调用时每次分配独立实例
- 不需要手动初始化
- 运行速度比静态变量慢
- 但如果在函数内使用 RESTART 重新执行函数的话，不会被重置

## 常量（CONST）

一维数组常量，定义时必须给出所有初始值，定义后不可修改。

```
#DIM CONST HOGE = 1,2,3
#DIMS CONST SHOGE = "A", "B", "C"
```

- 必须同时设定初始值
- 不可后续赋值修改
- 不能与 `GLOBAL`、`SAVEDATA`、`REF`、`DYNAMIC` 同时使用
- 只能一维

## 引用类型变量（REF）

```
#DIM REF HOGE1DIM,0
#DIM REF HOGE2DIM,0,0
#DIM REF HOGE3DIM,0,0,0
#DIMS REF PUGE1DIM,0
#DIMS REF PUGE2DIM,0,0
#DIMS REF PUGE3DIM,0,0,0
```

- 不持有实际存储，操作它等于操作它所指向的变量
- 用于函数参数的引用传递（让函数内修改反映到调用侧）

## 函数体内动态定义（VARI / VARS）

`VARI`（整数型）和 `VARS`（字符串型）可以在函数体内的**任意位置**动态定义局部变量，无需像 `#DIM` 那样必须写在 `@函数名` 的下一行。

```
VARI 变量名 = 初始值
VARS 变量名 = 初始值
VARI 变量名, 数组大小      ; 定义数组，初始值为 0 或空字符串
VARS 变量名, 数组大小
```

- **作用域**：仅限当前函数内，行为类似 `#DIM DYNAMIC`（函数退出后自动释放）
- **无需前置声明**：写到哪就定义到哪，之后的代码即可使用
- **初始值**：整数型默认 `0`，字符串型**必须用 `"..."` 括起来**
- **数组大小**：省略时创建单个变量（非数组）

```
; 在函数体中间动态定义变量
@SOME_FUNC
    ; 先做一些处理
    PRINTL 开始处理...
    
    ; 在需要的地方直接定义
    VARS 问题 = "生命、宇宙以及一切的意义"
    VARI 答案 = 42
    PRINTFORML {问题}的答案是{答案}
    
    ; 定义数组
    VARI 数列, 4
    REPEAT 4
        数列:COUNT = COUNT * 14
        PRINTFORML 数列[{COUNT}] = {数列:COUNT}
    REND
```

> `VARI`/`VARS` 的值不会在函数多次调用间保留（每次调用都重新创建），如需跨调用保持值，请用函数头部的 `#DIM`（静态变量）。

## 为自定义数组元素命名（ERD / CSV 命名）

ERH 中用 `#DIM`/`#DIMS` 定义的数组变量，可以通过创建对应的 `.ERD` 或 `.csv` 文件为数组元素赋予字符串名称，之后就能像 `ABL:MASTER:"体力"` 一样用字符串名访问元素。

### 文件命名规则

| 变量维度 | 文件命名 | 示例 |
|----------|----------|------|
| 一维数组 | `变量名.ERD` | `HOGE.ERD` |
| 二维数组 | `变量名@1.ERD`（第一维）、`变量名@2.ERD`（第二维） | `HOGE2D@1.ERD` |
| 三维数组 | `变量名@1.ERD`、`变量名@2.ERD`、`变量名@3.ERD` | `HOGE3D@1.ERD` |

`@1`、`@2`、`@3` 分别对应从左到右的第 1、2、3 个下标维度。

### 文件格式

首先需要在一个 `ERB` 文件中定义数组变量，例如：

```
#DIM HOGE, 3
#DIM HOGE2D, 3, 3
#DIM HOGE3D, 3, 3, 3
```
这样就定义了一维数组 `HOGE`，二维数组 `HOGE2D`，三维数组 `HOGE3D`，本例中三个数组的每维长度都是3。

与普通 CSV 变量文件格式相同（如 `CFLAGNAME.csv`），放在 `ERB` 文件夹内：

```
; HOGE.ERD
0,记录0
1,记录1
2,记录2
```

定义后即可使用字符串名访问：

```
; 数值索引
HOGE:0 = 100

; 等价于字符串索引
HOGE:记录0 = 100
```

> 此方法定义的数组变量是和CSV中 `FLAG` 类似的，全局只有一份，不像 `CFLAG` 那样一个角色一个，也不能用 `HOGE:角色编号:记录0` 这样的形式访问。一个角色一个的还是只有 `CFLAG` 和 `CDFLAG`。

### 注意事项

- `.ERD` 文件放在 `ERB` 文件夹中（`.csv` 同理放在 `CSV` 文件夹中）
- 如果存在多个文件定义了相同的标识符（名称），启动时会报错
- 不同标识符可以映射到同一整数索引

## ERH 中的特殊关键字

在 ERH 中定义全局变量时可用以下关键字：

### SAVEDATA

可保存的变量：

```
#DIM SAVEDATA MY_SAVED, 100
#DIMS SAVEDATA MY_SAVED_STR, 50
```

保存/加载与 `DAY`、`MONEY` 等相同。
※定义可保存的多维变量时需启用「以二进制格式保存存档数据」

### CHARADATA

角色变量，只能写在 ERH 中：

```
#DIM CHARADATA C_INT, 100
#DIMS CHARADATA C_STR, 50
#DIM CHARADATA SAVEDATA CS_INT, 100   ; 可保存角色变量
```

上面的例子中，C_INT、C_STR是角色变量，但不会被保存加载。
CS_INT既是角色变量，也进行保存和加载。

使用方式与 `CFLAG` 等相同，如`C_INT:角色编号:0 = 10`

### GLOBAL(S)

跨存档全局变量：

```
#DIM GLOBAL G_INT, 100
#DIMS GLOBALS G_STR, 50
#DIM GLOBAL SAVEDATA GS_INT, 100      ; 可 SAVEGLOBAL/LOADGLOBAL
```

`GLOBAL` 为整数，`GLOBALS` 为字符串。
默认大小为`GLOBAL` 1000，`GLOBALS` 100，可通过 `VariableSize.csv` 更改。
不与其它数据一同保存和加载，即存档时不会自动保存。
要保存全局变量，需要使用 `SAVEGLOBAL` 指令,执行后，`GLOBAL` 和 `GLOBALS` 会被保存到 `sav/global.sav`。
而通过 `LOADGLOBAL` 则可从 `sav/global.sav` 读取 `GLOBAL` 和 `GLOBALS` 。
建议在 `@EVENTFIRST` 和 `@EVENTLOAD` 时机执行 `LOADGLOBAL`。
通过 `GLOBAL`、`GLOBALS` 变量可以在不同存档数据间共享数据。 

## 限制

1. 不能使用与命令同名（`PRINTFORM`、`SELECTCASE`、`CALL`、`RETURN`、`GOTO` 等）
2. 可以使用与函数/预处理同名但不推荐
3. 函数内 `#DIM` 可以在函数参数中使用：

```
@FUNC_NAME, MY_INT, MY_STR
#DIM MY_INT
#DIMS MY_STR
    ; 使用 MY_INT, MY_STR
```

4. 仅在函数中可使用 `DYNAMIC`、`REF`
5. ERH 中的元素个数可用常量表达式指定，但宏不展开
