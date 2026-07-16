# ERH 头文件

ERH（Era Header）文件放在 ERB 文件夹中，扩展名为 `.ERH`，在 ERB 之前被处理。

## 用途

ERH 文件只应包含：
1. `#DIM` / `#DIMS` 定义全局变量
2. `#DEFINE` 定义宏

**不要**在 ERH 中写其他可执行语句。

## 处理顺序

`csv文件` → `*.ERH` → `*.ERB`

- ERH 的效果不作用于 CSV 文件夹
- `_rename.csv` 的替换也适用于 `*.ERH`

## 全局变量的声明

```
<*.ERH>
    #DIM MY_INT
    #DIM MY_INT_ARRAY, 100
    #DIMS MY_STR
    #DIMS MY_STR_ARRAY, 100
```

ERB 中使用：

```
<*.ERB>
    MY_INT = 100
    MY_INT_ARRAY:10 = MY_INT_ARRAY:10 + 45
    MY_STR = 示例文字
    PRINTFORML {MY_INT_ARRAY:10} %MY_STR%
```

可定义最多三维数组。

## 关键字

### SAVEDATA

定义可保存的全局变量：

```
#DIM SAVEDATA MY_INT_ARRAY, 100
#DIMS SAVEDATA MY_STR_ARRAY, 100
```

保存/加载动作与 `DAY`、`MONEY` 等内建变量相同。

**注意**：`#DIMS SAVEDATA` 定义多维变量需开启「以二进制格式保存存档数据」。

### CHARADATA

定义角色变量：

```
#DIM CHARADATA C_INT_ARRAY, 100
#DIMS CHARADATA C_STR_ARRAY, 100
#DIM CHARADATA SAVEDATA CS_INT_ARRAY, 100
```

`C_INT_ARRAY:MASTER:0 = 10` 这样的方式使用。
可与 `SAVEDATA` 组合。

### GLOBAL

定义跨存档全局变量：

```
#DIM GLOBAL G_INT_ARRAY, 100
#DIMS GLOBAL G_STR_ARRAY, 100
#DIM GLOBAL SAVEDATA GS_INT_ARRAY, 100
```

全局变量在通常的存档/读档时不影响。
与 `SAVEDATA` 组合后通过 `SAVEGLOBAL`/`LOADGLOBAL` 读写 `global.sav`。

## 宏定义

```
#DEFINE MACRO_NAME
    ; 宏内容
#ENDDEFINE

; 带参数
#DEFINE MACRO_NAME(arg1, arg2)
    PRINTFORML {arg1} + {arg2}
#ENDDEFINE
```

详情参考 [函数与预处理器](./preprocessor.md)。

## 注意事项

1. ERH 中的 `#DIM` 不支持 `DYNAMIC`、`REF` 关键字
2. 元素个数可用常量表达式指定，但宏不展开
3. Emuera 读取所有 `*.ERH`；eramakerEX 不适用 `_rename.csv` 到 `*.ERH`
4. 使用 `*.ERH` 会失去与 `eramakerEX` 的兼容性
