# DataTable 命令

DataTable 是 Emuera EM 版新增的内存数据库功能。

## 基本管理

```
DT_CREATE 表名              ; 创建 DataTable（RESULT: 0=成功）
DT_EXIST 表名               ; 检查表是否存在（RESULT: 0=否/1=是）
DT_RELEASE 表名             ; 释放表
DT_CLEAR 表名               ; 清空表（保留列定义）
DT_NOCASE 表名, 0/1         ; 设置是否忽略大小写
```

## 列（Column）操作

```
DT_COLUMN_ADD 表名, 列名 [, 默认值, 类型]     ; 添加列
DT_COLUMN_EXIST 表名, 列名                    ; 检查列存在
DT_COLUMN_REMOVE 表名, 列名                   ; 删除列
DT_COLUMN_LENGTH 表名                         ; 获取列数
DT_COLUMN_OPTIONS 表名, 列名, 关键字, 值...   ; 设置列选项
DT_COLUMN_NAMES 表名 [, ref string[]]         ; 获取列名列表
```

列类型：
- 不指定 → 整数类型
- 字符串型 → 设定默认值为字符串

## 行（Row）操作

```
DT_ROW_ADD 表名 [, 列名, 值] ...                       ; 添加行（返回行号）
DT_ROW_ADD 表名, ref string[] 列名, ref any[] 值, int  ; 批量添加行
DT_ROW_SET 表名, 行号, 列名, 值 [, 列名, 值] ...       ; 设置行数据
DT_ROW_SET 表名, 行号, ref string[], ref any[], int    ; 批量设置
DT_ROW_REMOVE 表名, 行号                                ; 删除行
DT_ROW_REMOVE 表名, ref int[] 行号列表, int             ; 批量删除
DT_ROW_LENGTH 表名                                      ; 获取行数
```

## 单元格（Cell）操作

```
DT_CELL_GET 表名, 行号, 列名 [, 默认值]      ; 获取数值（int）
DT_CELL_GETS 表名, 行号, 列名 [, 默认值]     ; 获取字符串（string）
DT_CELL_SET 表名, 行号, 列名 [, 值, 选项]    ; 设置值
DT_CELL_ISNULL 表名, 行号, 列名 [, 选项]     ; 是否为空
```

## 查询

```
DT_SELECT 表名 [, 列名, 值, ref int[] 结果]    ; 条件查询
```

返回匹配的行号数组。

## 序列化

```
DT_TOXML 表名 [, ref string 输出]   ; 导出为 XML（返回 string）
DT_FROMXML 表名, XML字符串, 模式     ; 从 XML 导入
```

## 示例

```
; 创建物品表
DT_CREATE "items"

; 添加列
DT_COLUMN_ADD "items", "name", "未命名"
DT_COLUMN_ADD "items", "price", 0
DT_COLUMN_ADD "items", "stock", 0

; 添加行
DT_ROW_ADD "items", "name", "药草", "price", 50, "stock", 10
DT_ROW_ADD "items", "name", "解毒药", "price", 100, "stock", 5

; 查询
DT_SELECT "items", "stock", 0, results
; results 包含所有库存为 0 的行号

; 获取数据
name = DT_CELL_GETS("items", 0, "name")     ; "药草"
price = DT_CELL_GET("items", 0, "price")    ; 50

; 修改
DT_CELL_SET "items", 0, "stock", 20
```
