# CSV 格式参考

本文档说明 ERA Basic 中使用的各种 CSV 文件格式。

## CSV 基本规格

- 编码：Shift_JIS（eramaker）/ UTF-8（Emuera）
- 扩展名：`.CSV`
- 位置：放在 `CSV` 文件夹内
- 分隔符：半角逗号 `,`

## 主要 CSV 文件一览

### 角色定义（chara*.csv）

```
番号,名称,称呼,能力,1,2,3,...,素质,0,1,2,...,基础,100,200,...
```

支持：
- `chara01.csv`、`chara02.csv` 等（eramaker 兼容）
- `chara101.csv`、`charaABC.csv` 等（Emuera 扩展）
- 所有匹配 `chara*.csv` 的文件都会被加载

### 能力名称（abl.csv）

```
0,体力
1,气力
2,腕力
...
```

用于定义 `ABL` 的元素名。`ABLNAME` 可引用。

### 素质名称（talent.csv）

```
0,处女
1,敏感
2,痛觉
...
```

用于定义 `TALENT` 的元素名。`TALENTNAME` 可引用。

### 经验名称（exp.csv）

```
0,接吻
1,口交
...
```

用于定义 `EXP` 的元素名。`EXPNAME` 可引用。

### 参数名称（palam.csv）

```
0,快感
1,疼痛
...
```

用于定义 `PALAM`、`JUEL`、`GOTJUEL`、`UP`、`DOWN`、`CUP`、`CDOWN` 的元素名。

### 刻印名称（mark.csv）

```
0,阴蒂责罚
1,震动棒
...
```

用于定义 `MARK` 的元素名。`MARKNAME` 可引用。

### 基础名称（base.csv）

```
0,体力
1,魅力
2,教养
...
```

用于定义 `BASE`、`MAXBASE`、`LOSEBASE`、`DOWNBASE` 的元素名。

### 调教名称（train.csv）

```
0,爱抚
1,接吻
...
```

用于定义调教命令的名称。`TRAINNAME` 可引用。

### 物品（item.csv）

```
0,药草,200
1,解毒药,500
...
```

格式：`编号,名称,价格`

`ITEMNAME` 和 `ITEMPRICE` 可引用。

### 标志名称（flag.csv）

```
0,事件A已完成
1,事件B已完成
...
```

用于定义 `FLAG` 的元素名。最大 10000 元素。

### 字符串（str.csv）

```
0,你好
1,再见
...
```

定义 `STR` 的**值**（不是名称）。
`STRNAME` 由 `strname.csv` 定义。

### 字符串名称（strname.csv）

```
0,问候
1,告别
...
```

定义 `STR` 的**元素名**。最大 20000 元素。

**注意**：`str.csv` 和 `strname.csv` 是不同的文件！

### 角色字符串名称（cstr.csv）

定义 `CSTR` 的元素名。`CSTRNAME` 可引用。

### 回合字符串名称（tstr.csv）

定义 `TSTR` 的元素名。`TSTRNAME` 可引用。

### 存档字符串名称（savestr.csv）

定义 `SAVESTR` 的元素名。`SAVESTRNAME` 可引用。

### 角色 CSV 数据名称（cflag.csv）

```
0,恋爱度
1,信頼度
...
```

定义 `CFLAG` 的元素名。`CFLAGNAME` 可引用。

### 其他

| 文件 | 变量 | 说明 |
|------|------|------|
| `equip.csv` | `EQUIPNAME`, `EQUIP` | 装备名称和值 |
| `tequip.csv` | `TEQUIPNAME`, `TEQUIP` | 回合装着 |
| `stain.csv` | `STAINNAME` | 污渍名称 |
| `ex.csv` | `EXNAME`, `EX` | 绝顶经验名称 |
| `source.csv` | `SOURCENAME`, `SOURCE` | 调教来源名称 |
| `tcvar.csv` | `TCVARNAME` | 回合角色变量名称 |
| `cdflag1.csv` / `cdflag2.csv` | `CDFLAGNAME1` / `CDFLAGNAME2` | 三维标志名称 |
| `global.csv` | `GLOBALNAME` | グローバル变量名称 |
| `globals.csv` | `GLOBALSNAME` | グローバル字符串名称 |

## gamebase.csv

游戏基础信息文件：

```
作者,サークル名
追加情報,追加テキスト
製作年,2024
タイトル,ゲームタイトル
コード,12345
バージョン,100
バージョン違い認める,1
最初からいるキャラ,1
アイテムなし,0
ウィンドウタイトル,カスタムタイトル
```

对应 `GAMEBASE_*` 系列变量。

## VariableSize.csv

改变内建变量的元素个数：

```
变量名,元素数
FLAG,5000
TFLAG,2000
CFLAG,2000
```

- 设定 `-1` 可禁止该变量使用
- 禁止后，赋值和引用会报错
- 系统需要时返回 `-1`
- `COUNT` 禁止后 `REPEAT` 也不可使用
