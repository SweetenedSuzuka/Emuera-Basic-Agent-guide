# 图形命令

## 画布（Canvas）操作

### 创建/销毁

```
GCREATE 画布ID, 宽, 高            ; 创建画布
GCREATED 画布ID, 宽, 高, 数据     ; 创建带数据的画布
GCREATEFROMFILE 画布ID, 文件路径 [, 相对路径模式]  ; 从文件创建画布
GDISPOSE 画布ID                    ; 销毁画布
GCLEAR 画布ID [, 颜色]            ; 清空/填充画布
GCLEAR 画布ID, cARGB, x, y, width, height  ; 填充指定区域
```

- **GCLEAR**：书式1 清空/填充整个画布；书式2 只填充 `(x, y)` 为左上角、`width × height` 的矩形区域。
- **GCREATEFROMFILE**：第三参数为 `0` 或省略时从 `resources/` 文件夹加载；为非 `0` 时路径相对于 `Emuera.exe` 所在目录。
- **GSETFONT**：第四参数为字体样式位掩码——`1`=粗体、`2`=斜体、`4`=删除线、`8`=下划线，可组合（如 `9` = 粗体+下划线）。

### 绘制

```
GDRAWG 目标画布ID, 源画布ID, x, y                    ; 绘制画布
GDRAWGWITHMASK 目标, 源, mask, x, y                  ; 带遮罩绘制
GDRAWGWITHROTATE 目标, 源, x, y, 角度, 中心x, 中心y  ; 旋转绘制
GDRAWTEXT 画布ID, 字符串, x, y [, 颜色]              ; 绘制文字
GDRAWSPRITE 画布ID, 精灵ID, x, y                     ; 绘制精灵
GDRAWLINE 画布ID, x1, y1, x2, y2, 颜色               ; 绘制线段
GFILLRECTANGLE 画布ID, x, y, w, h [, 颜色]           ; 填充矩形
```

### 属性设置

```
GSETCOLOR 画布ID, r, g, b                       ; 设置颜色
GSETBRUSH 画布ID, r, g, b [, alpha]             ; 设置画刷
GSETPEN 画布ID, r, g, b, width                  ; 设置画笔
GSETFONT 画布ID, 字体名, 大小 [, 字符集] [, 样式]   ; 设置字体
GDASHSTYLE 画布ID, style, width, gap            ; 设置虚线样式
```

#### GDASHSTYLE — 画笔虚线样式

```
GDASHSTYLE 画布ID, 虚线样式, 实线长, 间隔长
```

设置画笔的虚线样式，影响后续 `GDRAWLINE` 等画线命令。

| 参数 | 说明 |
|------|------|
| `画布ID` | 目标画布编号 |
| `虚线样式` | `0`=实线、`1`=虚线、`2`=点线、`3`=点划线、`4`=双点划线 |
| `实线长` | 每段实线部分的长度（像素） |
| `间隔长` | 每段间隔部分的长度（像素） |

```
; 在画布 0 上设置虚线样式
GDASHSTYLE 0, 1, 5, 3
GDRAWLINE 0, 10, 10, 100, 100, 0xFF0000
; 画出一条红色虚线
```

### 属性获取

```
GGETCOLOR 画布ID        ; 返回颜色（int）
GGETBRUSH 画布ID        ; 返回画刷颜色（int）
GGETFONT 画布ID         ; 返回字体名（string）
GGETFONTSIZE 画布ID     ; 返回字体大小（int）
GGETFONTSTYLE 画布ID    ; 返回字体样式（int）
GGETPEN 画布ID          ; 返回画笔颜色（int）
GGETPENWIDTH 画布ID     ; 返回画笔宽度（int）
GGETTEXTSIZE 画布ID, 字符串  ; 返回文字的 (w, h)
```

### 尺寸

```
GWIDTHHEIGHT 画布ID     ; RESULT:0 = 宽, RESULT:1 = 高
```

### 保存/加载

```
GSAVELOAD 画布ID, 文件路径, mode   ; mode: 0=保存 1=加载
```

## 精灵（Sprite）操作

```
SPRITECREATE 精灵ID, 画布ID, srcX, srcY, w, h   ; 创建精灵
SPRITEDISPOSE 精灵ID                              ; 销毁精灵
SPRITEDISPOSEALL                                  ; 销毁所有精灵
SPRITEMOVE 精灵ID, x, y                           ; 移动精灵
SPRITESETPOS 精灵ID, x, y, 画布ID                 ; 设置精灵位置
SPRITEPOSXY 精灵ID                                ; RESULT:0=x, RESULT:1=y
SPRITEWIDTHHEIGHT 精灵ID                          ; RESULT:0=w, RESULT:1=h
SPRITEGETCOLOR 精灵ID                             ; 获取精灵颜色
```

### SPRITECREATED — 检查精灵是否已创建

```
int SPRITECREATED 精灵名
```

检查指定名称的精灵是否已被创建（调用过 `SPRITECREATE` 且未被 `SPRITEDISPOSE` 销毁）。

- 参数：`string`，精灵名称（即 `SPRITECREATE` 的第2个参数）
- 返回值：已创建返回 `1`，未创建或已销毁返回 `0`
- 命令形式和式中函数形式都支持

```
; 检查精灵是否已创建
SPRITECREATED "my_sprite"
SIF RESULT
    PRINT 精灵 my_sprite 已存在
```

## 精灵动画

```
SPRITEANIMECREATE 动画ID, 精灵ID     ; 创建动画
SPRITEANIMEADDFRAME 动画ID, 精灵ID   ; 添加帧
```

## 按钮背景图形（CButtonG）

```
CBGSETG 画布ID, x, y                           ; 设置按钮背景画布
CBGSETSPRITE 精灵ID, x, y                      ; 设置按钮背景精灵
CBGSETBMAPG                                     ; 应用按钮背景
CBGSETBUTTONSPRITE 按钮编号, 精灵ID, x, y       ; 设置单个按钮精灵
CBGCLEAR                                        ; 清除按钮背景
CBGCLEARBUTTON 按钮编号                         ; 清除单个按钮背景
CBGREMOVEMAPB                                   ; 移除按钮背景映射
CBGREMOVERANGE                                  ; 移除范围按钮背景
```

## 背景

```
SETBGIMAGE 文件路径 [, x, y]     ; 设置背景图像
REMOVEBGIMAGE 文件路径           ; 移除背景图像
CLEARBGIMAGE                     ; 清除所有背景图像
```
