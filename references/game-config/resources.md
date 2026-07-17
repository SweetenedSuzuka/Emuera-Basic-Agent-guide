# 资源文件

本文说明在 Emuera 中为显示图像而准备资源文件的方法。

## resources 文件夹

资源文件应放在可执行文件所在目录下的 `resources` 文件夹中。
文件只要在 `resources` 文件夹内，放在子文件夹中也无妨。

```
你的游戏目录/
├── emuera.exe
└── resources/
    ├── sprites.csv          # 资源指定 CSV
    ├── chara/
    │   ├── hero.png
    │   └── enemy.png
    └── bg/
        └── forest.jpg
```

---

## 资源指定 CSV 文件

在 `resources` 文件夹中放置 CSV 格式的文本文件，它将被作为资源指定文件读入。
格式如下：

### 注释行

以分号（`;`）开头的行被视为注释，忽略不处理。

```
; 这是一条注释，不会被解析
```

### 精灵（Sprite）

```
リソース名, 元ファイル名, x, y, width, height, posx, posy
```

| 字段 | 含义 | 是否可省略 |
|---|---|---|
| `リソース名` | 资源的唯一名称，用于 `<img src='...'>` 的 `src` 属性值，也用于 `SPRITECREATED("...")` 等。不得与其他资源名重复。 | 不可省略 |
| `元ファイル名` | 图像文件名（含扩展名）。使用相对于 CSV 文件的路径。不能引用 CSV 所在目录的上层；只能指定同级或子文件夹中的图像。 | 不可省略 |
| `x` | 从原图左上角起，截取区域的 X 坐标（像素）。 | 可省略（默认 0） |
| `y` | 从原图左上角起，截取区域的 Y 坐标（像素）。 | 可省略（默认 0） |
| `width` | 截取宽度（像素）。 | 可省略（默认图像宽度） |
| `height` | 截取高度（像素）。 | 可省略（默认图像高度） |
| `posx` | 绘制时的相对 X 偏移。可通过 `SPRITEPOS` 或 `SPRITEMOVE` 指令动态修改。 | 可省略（默认 0） |
| `posy` | 绘制时的相对 Y 偏移。可通过 `SPRITEPOS` 或 `SPRITEMOVE` 指令动态修改。 | 可省略（默认 0） |

**省略 `x, y, width, height` 时**：使用整张原图。

**省略 `posx, posy` 时**：等同于指定 `0, 0`。

#### 精灵示例

CSV 内容：
```
; 精灵定义示例
hero_stand, chara/hero.png, 0, 0, 64, 64, 0, 0
hero_walk,  chara/hero.png, 64, 0, 64, 64, 0, 0
enemy,      chara/enemy.png, , , , , 0, 0
bg_forest,  bg/forest.jpg
```

说明：
- `hero_stand` 截取 `hero.png` 左上角 64×64 区域作为精灵。
- `hero_walk` 截取同一张图中右侧相邻的 64×64 区域。
- `enemy` 省略了 `x, y, width, height` 但保留了位置分隔符——实际等于使用 `enemy.png` 整张图。
- `bg_forest` 完全省略尾部字段，使用 `forest.jpg` 整张图，偏移默认为 0。

ERB 中的使用：
```erb
; 在 HTML 中显示精灵
PRINT <img src='hero_stand'>

; 通过脚本创建精灵对象
SPRITECREATE "hero_stand", 0
```

---

### 动画精灵（Anime Sprite）

```
リソース名, ANIME, width, height
リソース名, 元ファイル名, x, y, width, height, offsetx, offsety, delay
リソース名, 元ファイル名, x, y, width, height, offsetx, offsety, delay
……
```

创建动画精灵需先写一行以 `ANIME` 替代文件名的定义行，指定精灵的整体尺寸。
此处的 `width, height` 必须为正整数，不可省略。

随后各行定义动画的每一帧，格式与普通精灵类似，但额外多一个 `delay` 字段：

| 字段 | 含义 | 是否可省略 |
|---|---|---|
| `delay` | 该帧显示的时间，单位毫秒（ms）。省略时默认 `1000`（即 1 秒）。 | 可省略（默认 1000） |

> **注意**：Emuera 默认在 `INPUT` 等等待输入期间不执行重绘，因此动画精灵会看起来卡在某一帧不动。
> 请执行 `SETANIMETIMER` 指令，让 INPUT 等待时也刷新动画帧。

#### 动画精灵示例

CSV 内容：
```
; 一个 4 帧的行走动画
player_walk, ANIME, 64, 64
player_walk, chara/player.png,   0, 0, 64, 64, 0, 0, 200
player_walk, chara/player.png,  64, 0, 64, 64, 0, 0, 200
player_walk, chara/player.png, 128, 0, 64, 64, 0, 0, 200
player_walk, chara/player.png, 192, 0, 64, 64, 0, 0, 200
```

说明：
- 第一行 `ANIME` 声明创建名为 `player_walk` 的动画精灵，整体尺寸 64×64。
- 后续 4 行各定义一帧，每帧显示 200ms，形成循环动画（4 帧 × 200ms = 800ms 一轮）。

ERB 中的使用：
```erb
; 让 INPUT 等待时也刷新动画
SETANIMETIMER 1

; 显示动画精灵
PRINT <img src='player_walk'>

; 通过脚本创建动画精灵对象
SPRITECREATE "player_walk", 0
```

---

## 图像文件

显示图像需要准备图像文件。
图像文件需为 **bmp**、**jpg**、**png**、**webp** 格式之一，放在 `resources` 文件夹内。
- 在 ERB 中也可使用 `GCREATEFROMFILE` 指令动态生成图形对象。

---

## 内存注意事项

- **CSV 文件中指定的所有图像在 Emuera 启动时全部加载到内存**，并在进程结束前一直占用。
- 大量小图散落不如将图像合并为单张大图（精灵表/Sprite Sheet），再通过 `x, y, width, height` 截取——这在内存和速度上都更有利。
- 按需动态加载/释放也很有效：

  ```erb
  ; 动态创建图形
  GCREATEFROMFILE "temp_bg", "resources/special_bg.png"
  ; … 使用 temp_bg 进行绘制 …
  ; 用完释放
  GDISPOSE "temp_bg"

  ; 动态创建精灵
  SPRITECREATE "temp_sprite", 0
  ; … 使用 temp_sprite …
  ; 用完释放
  SPRITEDISPOSE "temp_sprite"
  ```

---

## 绘图接口

Emuera 的绘图后端取决于配置中的绘图接口设置：

| 配置值 | 底层引擎 | Alpha 混合 |
|---|---|---|
| `WINAPI` | GDI | 不支持 |
| `Graphics` | GDI+ | 支持 |
| `TextRenderer` | GDI+ | 支持 |

- `WINAPI`（GDI）下不进行 alpha 混合，透明区域直接显示为背景色。
- `Graphics` 或 `TextRenderer`（GDI+）下支持 alpha 混合，可实现半透明叠加效果。
- 图像的缩放行为在 GDI 与 GDI+ 之间也略有差异。

### SETANIMETIMER 指令

```erb
; 语法
SETANIMETIMER interval
; interval: 定时器间隔（毫秒），设为 0 则停止动画刷新
```

让 Emuera 在 `INPUT` 等等待输入期间也定期刷新画面，从而使动画精灵正常播放。请于使用动画精灵前调用。
