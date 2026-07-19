# MAP（关联数组）命令

MAP 系列指令用于键值存储功能。

## 基本管理

```
MAP_CREATE 映射名      ; 创建 MAP（RESULT: 0=成功/非0=已存在）
MAP_EXIST 映射名       ; 检查 MAP 是否存在（RESULT: 0=否/1=是）
MAP_RELEASE 映射名     ; 释放 MAP
```

## 键值操作

```
MAP_GET 映射名, 键名           ; 获取值（返回 string）
MAP_HAS 映射名, 键名           ; 检查键是否存在（RESULT: 0=否/1=是）
MAP_SET 映射名, 键名, 值       ; 设置键值（RESULT: 0=成功）
MAP_REMOVE 映射名, 键名        ; 删除键（RESULT: 0=成功）
```

## 管理操作

```
MAP_SIZE 映射名               ; 获取键数（RESULT）
MAP_CLEAR 映射名              ; 清空（RESULT: 0=成功）
```

## 获取所有键

```
MAP_GETKEYS 映射名                  ; 获取所有键（返回字符串，逗号分隔）
MAP_GETKEYS 映射名, int             ; 获取键列表
MAP_GETKEYS 映射名, ref string[], int ; 获取键数组
```

## 序列化

```
MAP_TOXML 映射名          ; 导出为 XML（返回 string）
MAP_FROMXML 映射名, XML   ; 从 XML 导入（RESULT: 0=成功）
```

## 示例

```
; 创建全局状态存储
MAP_CREATE "global_flags"

; 设置值
MAP_SET "global_flags", "seen_ending_a", "1"
MAP_SET "global_flags", "highest_score", "5000"

; 获取值
score_str = MAP_GET("global_flags", "highest_score")
score = TOINT(score_str)

; 检查键
MAP_HAS "global_flags", "seen_ending_a"
SIF RESULT
    PRINTL 已看过结局 A

; 获取所有键
keys = MAP_GETKEYS("global_flags")
PRINTFORML 所有键：{keys}

; 保存/加载
xml_str = MAP_TOXML("global_flags")
; ... 保存到文件 ...
MAP_FROMXML "global_flags_loaded", xml_str
```
