# XML 命令

XML 命令群提供基于 .NET 标准 XPath 的 XML 处理功能。

### XPath 支持

Emuera 支持标准 XPath 1.0 的子集：
- 路径：`/root/element`
- 属性：`/@attr`
- 通配符：`*`、`//`
- 索引：`[1]`（1-based）、`[last()]`
- 条件：`[@attr='value']`

不支持复杂的 XPath 函数和轴选择。

## 文档管理

```
XML_DOCUMENT 变量, XML字符串     ; 解析 XML 并绑定到变量
XML_RELEASE 变量                 ; 释放 XML 文档
XML_EXIST 变量                   ; 检查文档是否存在
```

## 获取值

```
XML_GET 文档变量, XPath [, 索引, 索引2]                   ; 获取数值
XML_GET 文档变量, XPath, ref string[], 索引               ; 获取字符串数组
XML_GET_BYNAME 文档路径, XPath [, 索引, 索引2]            ; 通过名称获取
```

返回匹配的节点数存入 `RESULT`，而实际获取的值通过参数获取（如 `ref` 数组或后续参数）。

`索引` 参数指定当 XPath 匹配多个节点时取第几个（省略时取第 1 个）。`索引2` 用于二重数组场景。

## 设置值

```
XML_SET 文档变量, XPath, 值 [, 索引, 索引2]                ; 设置节点值
XML_SET ref 文档字符串, XPath, 值 [, 索引, 索引2]           ; 设置并返回
XML_SET_BYNAME 文档路径, XPath, 值                          ; 通过名称设置
```

## 节点操作

```
XML_ADDNODE 文档变量, XPath, 值 [, 索引, 索引2]            ; 添加节点
XML_ADDNODE ref 文档字符串, XPath, 值 [, 索引, 索引2]       ; 添加并返回
XML_ADDNODE_BYNAME 文档路径, XPath, 值                      ; 通过名称添加
XML_REMOVENODE 文档变量, XPath [, 索引]                     ; 删除节点
XML_REMOVENODE ref 文档字符串, XPath [, 索引]               ; 删除并返回
XML_REMOVENODE_BYNAME 文档路径, XPath [, 索引]              ; 通过名称删除
XML_REPLACE 文档变量, XPath, 值 [, 索引]                    ; 替换节点
XML_REPLACE_BYNAME 文档路径, XPath, 值 [, 索引]             ; 通过名称替换
```

## 属性操作

```
XML_ADDATTRIBUTE 文档变量, XPath, 属性名, 值 [, ns, idx, idx2]        ; 添加属性
XML_ADDATTRIBUTE ref 文档字符串, XPath, 属性名, 值 [, ns, idx, idx2]   ; 添加并返回
XML_ADDATTRIBUTE_BYNAME 文档路径, XPath, 属性名, 值 [, ns, idx, idx2]  ; 通过名称添加
XML_REMOVEATTRIBUTE 文档变量, XPath [, 索引]                           ; 删除属性
XML_REMOVEATTRIBUTE ref 文档字符串, XPath [, 索引]                     ; 删除并返回
XML_REMOVEATTRIBUTE_BYNAME 文档路径, XPath [, 索引]                    ; 通过名称删除
```

## 字符串转换

```
XML_TOSTR 文档变量     ; 将 XML 文档转为字符串
```

## 示例

```
; 解析 XML
xml_str = "<root><item id='1'>药草</item><item id='2'>解毒药</item></root>"
XML_DOCUMENT doc, xml_str

; 获取值
count = XML_GET(doc, "/root/item")
; RESULT = 2 (2 个 item 节点)

; 获取属性
XML_GET(doc, "/root/item/@id")
; 获取 id 属性

; 添加新节点
XML_ADDNODE doc, "/root/item", "回复药"
XML_ADDATTRIBUTE doc, "/root/item[last()]", "id", "3"

; 输出
new_xml = XML_TOSTR(doc)
PRINTFORML {new_xml}

; 释放
XML_RELEASE doc
```
