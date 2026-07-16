# 图形命令

## 画布（Canvas）操作

### 创建/销毁

```
GCREATE 画布ID, 宽, 高            ; 创建画布
GCREATED 画布ID, 宽, 高, 数据     ; 创建带数据的画布
GCREATEFROMFILE 画布ID, 文件路径  ; 从文件创建画布
GDISPOSE 画布ID                    ; 销毁画布
GCLEAR 画布ID [, 颜色]            ; 清空/填充画布
```

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
GSETFONT 画布ID, 字体名, 大小 [, style]          ; 设置字体
GDASHSTYLE 画布ID, style, width, gap            ; 设置虚线样式
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
CBGSETBUTTONSPRITE 按钮番号, 精灵ID, x, y       ; 设置单个按钮精灵
CBGCLEAR                                        ; 清除按钮背景
CBGCLEARBUTTON 按钮番号                         ; 清除单个按钮背景
CBGREMOVEMAPB                                   ; 移除按钮背景映射
CBGREMOVERANGE                                  ; 移除范围按钮背景
```

## 背景

```
SETBGIMAGE 文件路径 [, x, y]     ; 设置背景图像
REMOVEBGIMAGE 文件路径           ; 移除背景图像
CLEARBGIMAGE                     ; 清除所有背景图像
```
