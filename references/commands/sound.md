# 音频命令

## 音效（SE）

```
PLAYSOUND 文件路径     ; 播放音效
STOPSOUND              ; 停止音效
```

## 背景音乐（BGM）

```
PLAYBGM 文件路径       ; 播放 BGM
STOPBGM                ; 停止 BGM
```

## 音量

```
SETSOUNDVOLUME 音量    ; 设置音效音量（0-100）
SETBGMVOLUME 音量      ; 设置 BGM 音量（0-100）
```

## 文件检查

```
EXISTSOUND 文件路径     ; 检查音效文件是否存在
                        ; RESULT: 0=不存在 1=存在
```

## 使用示例

```
; 播放 BGM
PLAYBGM bgm_01.ogg

; 播放音效
PLAYSOUND se_click.wav

; 设置音量
SETBGMVOLUME 80
SETSOUNDVOLUME 100

; 检查文件
EXISTSOUND bgm_01.ogg
SIF RESULT == 0
    PLAYBGM bgm_default.ogg

; 停止
STOPBGM
STOPSOUND
```
