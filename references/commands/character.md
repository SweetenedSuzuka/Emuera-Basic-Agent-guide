# 角色操作命令

## 角色添加与删除

### ADDCHARA

```
ADDCHARA charaNo(, charaNo, ...)
```

从 `chara*.csv` 中读取指定编号的角色定义并添加角色。可一次添加多个。

- 参数为角色定义编号（`chara*.csv` 中的 `番号` 列，即登记编号）
- Emuera 支持一次传入多个角色定义编号

```
; 添加编号为 0 和 1 的角色
ADDCHARA 0, 1
```

### ADDCOPYCHARA

```
ADDCOPYCHARA charaID
```

复制指定角色的数据，创建一个新角色。

- 仅一个参数：被复制的源角色 ID
- 新角色自动追加到列表末尾

### ADDDEFCHARA

```
ADDDEFCHARA
```

执行游戏开始时的系统级角色添加处理。**无参数**，只能在 `@SYSTEM_TITLE` 中使用。

- 从 `chara0*.csv` 中读取并添加角色
- 从 `gamebase.csv` 的 `最初からいるキャラ` 指定的角色也会被添加
- 若对应 CSV 不存在，则创建空角色（同 ADDVOIDCHARA）

### ADDVOIDCHARA

```
ADDVOIDCHARA
```

**无参数**。创建一个所有变量值为 `0` 或空字符串的空角色。

### DELCHARA

```
DELCHARA charaID(, charaID, ...)
```

删除指定登记编号（列表位置）的角色。Emuera 支持一次删除多个。与 ADDCHARA 不同，DELCHARA 的参数是列表位置 ID 而非 CSV 定义编号。

### DELALLCHARA

```
DELALLCHARA
```

删除全部角色。

## 角色复制与交换

### COPYCHARA

```
COPYCHARA 源角色编号, 目标角色编号
```

复制角色数据（覆盖目标）。

### SWAPCHARA

```
SWAPCHARA 角色编号A, 角色编号B
```

交换两个角色的所有数据。

## 角色查找

### FINDCHARA / FINDLASTCHARA

```
FINDCHARA charaVariable, value(, startID, endID)
FINDLASTCHARA charaVariable, value(, startID, endID)
```

通过角色变量的值查找角色。第1参数是**角色变量**（如 `CFLAG:10`），第2参数是要查找的值。

- `FINDCHARA` 返回第一个匹配的角色 ID
- `FINDLASTCHARA` 返回最后一个匹配的角色 ID
- 未找到时返回 `-1`
- 第3参数可指定搜索起始位置，第4参数指定搜索结束位置
- 搜索范围（startID ~ endID）超出角色数范围时会报错
- 同时支持命令形式（结果存入 `RESULT`）和式中函数形式（直接返回值）

```
; 查找 CFLAG:10 等于 123 的所有角色并打印名字
X = -1
WHILE 1
    FINDCHARA CFLAG:10, 123, X + 1
    X = RESULT
    SIF X < 0
        BREAK
    PRINTFORML %NAME:X%
WEND
```

### PICKUPCHARA

```
PICKUPCHARA charaID(, charaID, ...)
```

**保留**参数中指定的角色，**删除**其他所有角色。`MASTER`、`TARGET`、`ASSI` 等变量会自动调整。

- 指定负值角色 ID 会报错，但若 `MASTER`、`TARGET`、`ASSI` 等变量本身为负值则例外（被忽略，不报错）

```
; 只保留角色 0 和 3，删除其余
PICKUPCHARA 0, 3
```

### GETCHARA

```
GETCHARA charaNO
```

判断指定角色定义编号的角色是否在当前列表中存在。存在则返回该角色在列表中的位置（从 0 开始），不存在返回 `-1`。

- 同时支持命令形式和式中函数：`int GETCHARA charaNO`

### SORTCHARA

```
SORTCHARA charaVariable, FORWARDorBACK
```

按指定角色变量排序角色列表。

- 第1参数 `charaVariable` 可省略（省略时按角色编号 `NO` 排序）
- 第2参数 `FORWARD`（升序）或 `BACK`（降序），可省略（默认升序）
- 排序键可以是字符串变量（如 `NAME`）、数值变量（如 `NO`）或数组变量（如 `CFLAG:0`）
- `MASTER` 不参与排序
- `TARGET:0`、`ASSI:0` 自动追随排序结果，无需手动调整
- 若使用了 `TARGET:1` 等变体，需手动追随

## 角色数据检查

### CHKCHARADATA

```
CHKCHARADATA 角色编号, CSV文件名, 键名
```

检查角色 CSV 数据。结果存入 `RESULT`。

### FIND_CHARADATA

```
FIND_CHARADATA CSV文件名, 键名, 值
```

通过 CSV 数据查找角色。结果存入 `RESULT`。

### CHKDATA

```
CHKDATA 角色编号, 数据项名, 值
```

检查指定角色数据项。

## 其他角色命令

### DOTRAIN

```
DOTRAIN 角色编号, 命令编号
```

对指定角色执行调教命令。

### CUPCHECK

```
CUPCHECK
```

检查角色 CUP 值（角色版 UPCHECK）。

### UPCHECK

```
UPCHECK
```

检查 UP（上升量）值。
