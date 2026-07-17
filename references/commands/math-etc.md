# 数学与其他命令

## 数学

### RAND / RANDOMIZE

```
RAND 上限                ; 0～上限-1 的随机数（存入 RESULT）
RAND 下限, 上限           ; 下限～上限-1 的随机数
RANDOMIZE                ; 初始化随机数种子
```

### ABS

```
ABS 值                   ; 绝对值（存入 RESULT）
A = ABS(X)               ; 式中函数形式
```

### MAX / MIN

```
MAX 值1, 值2             ; 最大值（存入 RESULT）
MIN 值1, 值2             ; 最小值（存入 RESULT）
```

### SQRT / POWER

```
SQRT 值                  ; 平方根
POWER 底, 指数           ; 幂运算
```

### 数学扩展

```
; 三角函数（EM+EE）
SIN / COS / TAN 角度
; 对数
LOG / LOG10 值
```

## 数组操作

### ARRAYSORT / ARRAYMSORT

```
ARRAYSORT 变量名 [, 开始, 长度, 方向]   ; 排序
ARRAYMSORT 变量名 [, 排序键, 方向]      ; 多维排序
ARRAYMSORTEX                           ; 多维排序扩展
```

方向：0=升序 / 1=降序

### ARRAYCOPY

```
ARRAYCOPY 目标变量, 源变量 [, 目标开始, 源开始, 长度]
```

复制数组内容。

### ARRAYSHIFT

```
ARRAYSHIFT 变量名 [, 开始, 长度, 移动量]
```

移动数组元素。

### ARRAYREMOVE

```
ARRAYREMOVE 变量名, 索引 [, 长度]
```

删除数组元素。

### SUMARRAY

```
SUMARRAY 变量名 [, 开始, 长度]     ; 求和
```

### MAXMINARRAY

```
MAXMINARRAY 变量名, 类型 [, 开始, 长度]   ; 最大值/最小值
; 类型: 0=最大值 1=最小值
```

### INRANGEARRAY

```
INRANGEARRAY 变量名, 最小值, 最大值 [, 开始, 长度]   ; 范围内元素计数
```

## 变量引用

### VARSIZE

```
VARSIZE 变量名           ; 获取变量元素数
; RESULT:0 = 第1维大小，RESULT:1 = 第2维大小
```

### ISDEFINED

```
ISDEFINED 变量名          ; 变量是否定义（RESULT: 0=否/1=是）
```

### EXISTVAR

```
EXISTVAR 变量名           ; 变量是否存在
```

### ENUMVAR / ENUMFUNC / ENUMMACRO

```
ENUMVAR 索引              ; 枚举变量名（返回 string）
ENUMFUNC 索引             ; 枚举函数名
ENUMMACRO 索引            ; 枚举宏名
```

### EXISTFUNCTION / EXISTMETH

```
EXISTFUNCTION 函数名     ; 函数是否存在
EXISTMETH 变量, 方法名   ; 方法是否存在
```

### STRDATA

```
STRDATA 变量名           ; 将字符串数据赋值到 CPU 内存
```

### RESET_STAIN

```
RESET_STAIN               ; 重置 STAIN 值
```

## 其他

### CONVERT

```
CONVERT 值, 从单位, 到单位
```

单位换算。

### FINDCHARA 相关

已涵盖在 [角色操作命令](./character.md) 中。

### TOOLTIP 扩展

```
TOOLTIP_SET 字符串        ; 设置工具提示
TOOLTIP_SETCOLOR 颜色     ; 设置工具提示颜色
```

### CLIENTFIELD

```
CLIENTWIDTH              ; 获取客户区宽度（int）
CLIENTHEIGHT             ; 获取客户区高度（int）
```

### EXISTFILE / ENUMFILES

```
EXISTFILE 文件路径       ; 检查文件存在（1=有/0=无）
ENUMFILES 目录, 模式 [, 计数]   ; 枚举文件
```

### EXISTCSV

```
EXISTCSV CSV文件名        ; 检查 CSV 加载状态
```
