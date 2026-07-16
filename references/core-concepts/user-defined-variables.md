# 用户定义变量

`#DIM`（整数类型）和 `#DIMS`（字符串类型）用于定义自定义变量。

## 定义位置与作用域

ERA 没有类，所有函数全局平级。变量只有两种：

| 定义位置 | 作用域 | 约束 |
|----------|--------|------|
| ERH 文件内 | 全局变量，所有函数均可访问 | 可以写 `#DIM` / `#DIMS` 和 `#DEFINE` |
| 函数声明的紧邻下一行 | 局部变量，仅在该函数内有效 | 一行一个，只能写 `#DIM` 和 `#DIMS`，且必须写在 `@` 函数定义的下一行，不能写在代码中间 |

> **不能**在 ERB 文件顶部或函数之间写变量。那些行不属于任何函数，会变成孤立行直接报错。ERA 没有文件级变量。  
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

一次元数组常量，定义时必须给出所有初始值，定义后不可修改。

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

使用方式与 `CFLAG` 等相同：`C_INT:MASTER:0 = 10`

### GLOBAL

跨存档全局变量：

```
#DIM GLOBAL G_INT, 100
#DIMS GLOBAL G_STR, 50
#DIM GLOBAL SAVEDATA GS_INT, 100      ; 可 SAVEGLOBAL/LOADGLOBAL
```

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
