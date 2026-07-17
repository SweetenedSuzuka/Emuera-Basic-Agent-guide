# 存档读档命令详解

## SAVEGAME / LOADGAME

```
SAVEGAME
LOADGAME
```

**均无参数**，只能在 `SHOP` 阶段调用。

- `SAVEGAME` — 打开存档画面
- `LOADGAME` — 打开读档画面

### 存档流程

1. `@SAVEINFO` 被调用 → `SAVEDATA_TEXT` 被设置为当前时间
2. 可自定义 `@SAVEINFO` 修改 `SAVEDATA_TEXT`
3. `SAVEDATA_TEXT` 被保存到存档文件

## SAVEDATA / LOADDATA / RESETDATA

```
SAVEDATA saveID, saveInfo
LOADDATA saveID
RESETDATA
```

- `saveID`：整数型存档槽号
- `saveInfo`：字符串型存档注释
- `SAVEDATA` 不会调用 `@SAVEINFO`，所以需通过第2参数 `saveInfo` 指定注释
- `SAVEDATA` 不会进行覆盖确认，如需确认请先用 `CHKDATA` 检查
- `LOADDATA` 加载指定槽号的数据。**加载失败会导致错误终止，必须先使用 CHKDATA 确认可加载后再执行**
- `RESETDATA` 重置所有数据到初始状态

```
; 保存到槽号 14，注释为当前日期
GETTIME
STR:0 = %RESULTS:0% 第{DAY+1}天
SAVEDATA 14, STR:0
```

## SAVEGLOBAL / LOADGLOBAL / RESETGLOBAL

```
SAVEGLOBAL      ; 写入 global.sav
LOADGLOBAL      ; 从 global.sav 读取
RESETGLOBAL     ; 重置全局变量
```

用于跨存档数据共享：

- `GLOBAL`、`GLOBALS` 变量
- `#DIM GLOBAL SAVEDATA` / `#DIMS GLOBAL SAVEDATA` 定义的变量

推荐调用时机：`@EVENTFIRST` 和 `@EVENTLOAD` 中调用 `LOADGLOBAL`。

## SAVECHARA / LOADCHARA

```
SAVECHARA filename, memo, charaNO(, charaNO2, ...)
LOADCHARA filename
```

**SAVECHARA**：
- 第1参数 `filename`（string）：文件名（实际保存为 `chara_*.dat`）
- 第2参数 `memo`（string）：存档备注，可通过 `CHKCHARADATA` 读取
- 第3参数起（int，可多个）：要保存的角色登记编号（列表位置 ID）
- dat 文件夹不存在时会自动创建

**LOADCHARA**：
- 仅一个参数 `filename`（string）：文件名
- 成功返回 `1`（存入 `RESULT:0`），失败返回 `0`
- 加载的角色会追加为新角色，不影响已有角色
- 新增角色数量 = 加载前后 `CHARANUM` 的差值

## SAVETEXT / LOADTEXT

```
int SAVETEXT text, fileNo(, forceSavdir, forceUTF8)
LOADTEXT fileNo(, force_savdir, force_UTF8)
```

**SAVETEXT**：
- 第1参数 `text`（string）：要保存的文本内容
- 第2参数 `fileNo`（int）：文件编号，实际文件名为 `textXX.txt`
- 第3参数 `forceSavdir`（int，可选）：非0时强制保存到 sav 文件夹
- 第4参数 `forceUTF8`（int，可选）：非0时强制使用 UTF-8 编码
- 返回值：成功非0，失败0
- 短时间内反复写入同一文件可能因防病毒软件导致失败，务必检查返回值

**LOADTEXT**：
- 第1参数 `fileNo`（int）：文件编号
- 第2参数 `force_savdir`（int，可选）：非0时强制从 sav 文件夹读取
- 第3参数 `force_UTF8`（int，可选）：非0时按 UTF-8 编码读取
- 命令形式：结果存入 `RESULTS:0`
- 式中函数形式：直接返回读取结果
- 失败时 `RESULTS:0` 为空字符串

> EM+EE 中，若第1参数为字符串，则作为文件路径直接读写（需在 emuera.config 中配置允许的扩展名）。

## CHKDATA / DELDATA

```
int CHKDATA saveID
DELDATA saveID
```

**CHKDATA**：
- `saveID`：整数型存档槽号
- 返回值（存入 `RESULT:0`）：
  - `0` — 可加载（此时 `RESULTS:0` 为存档注释）
  - `1` — 文件不存在（此时 `RESULTS:0` 为错误信息）
  - `2` — 游戏代码不同（`gamebase.csv` 的 `コード` 值不一致）
  - `3` — 版本不同（`gamebase.csv` 的 `バージョン` 值不一致且未允许）
  - `4` — 其他问题
- 独立命令调用（非赋值/非式中函数上下文）时，`RESULT:0` 获得存档时间戳而非状态码

**DELDATA**：
- `saveID`：整数型存档槽号
- 删除指定槽号的存档文件。文件不存在时也不会报错

## SAVENOS

```
int SAVENOS variable
```

获取「表示するセーブデータ数」配置项的值（默认为 20）。**参数不可省略**。

- 命令形式：将值存入指定的变量
- 式中函数形式：直接返回值
- 等价于 `GETCONFIG("表示するセーブデータ数")`

## LASTLOAD 变量

| 变量 | 类型 | 说明 |
|------|:--|------|
| `LASTLOAD_VERSION` | int（只读） | 上次加载的存档版本号 |
| `LASTLOAD_NO` | int（只读） | 上次加载的存档编号 |
| `LASTLOAD_TEXT` | str（只读） | 上次加载的存档文本 |

LOADDATA 后更新，RESETDATA 或「返回标题」后恢复初始值（-1 或空）。

## SAVEDATA_TEXT

可读写的字符串变量，在 `@SAVEINFO` 调用时被设定为当前时间。
可在 `@SAVEINFO` 中修改以自定义存档描述。

## PUTFORM

```
PUTFORM 格式化字符串
```

追加文本到 `SAVEDATA_TEXT`，用于自定义存档画面的注释。

## 存档数据格式

- 默认使用文本格式，可选择配置项「セーブデータをバイナリ形式で保存する」切换为二进制格式
- 二进制格式支持 `#DIMS SAVEDATA` 的多维保存
