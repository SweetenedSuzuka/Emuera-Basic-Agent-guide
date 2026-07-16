# 音频命令

音频文件放在 `sound/` 文件夹内。Emuera 使用 Windows Media Player 组件（WMPLib）播放音频，因此支持 WMP 可播放的全部格式（WAV、MP3、WMA 等；OGG 需要系统安装对应解码器）。

## PLAYSOUND — 播放音效

```
PLAYSOUND 文件名
```

播放 `sound/` 文件夹内的音频文件一次。**同时最多 10 个音效**。

```
PLAYSOUND se_click.wav
PLAYSOUND se_confirm.mp3
```

## STOPSOUND — 停止音效

```
STOPSOUND
```

停止**所有**正在播放的音效。无法单独停止某一个。

## PLAYBGM — 播放背景音乐

```
PLAYBGM 文件名
```

播放 `sound/` 文件夹内的音频文件，**自动循环**。再次调用 `PLAYBGM` 会切换到新曲目。

```
PLAYBGM bgm_field.ogg
```

## STOPBGM — 停止背景音乐

```
STOPBGM
```

停止正在播放的 BGM。

## 音量控制

```
SETSOUNDVOLUME 音量    ; 音效音量（0～100）
SETBGMVOLUME 音量      ; BGM 音量（0～100）
```

音量为 0 时静音，100 为最大。

## EXISTSOUND — 检查音效文件

```
int EXISTSOUND(文件名)
```

检查 `sound/` 文件夹内是否存在指定文件。存在返回 1，不存在返回 0。

## 完整示例

```
; 进入场景时切换 BGM
PLAYBGM bgm_dungeon.ogg
SETBGMVOLUME 75

; 检查文件存在再播放
SIF EXISTSOUND("bgm_boss.ogg")
    PLAYBGM bgm_boss.ogg

; 播放音效
PLAYSOUND se_sword.wav
PLAYSOUND se_damage.wav

; 淡出 BGM
SETBGMVOLUME 50
; ...场景结束...
STOPBGM
STOPSOUND
```

## 注意事项

- **文件路径**：仅需文件名，引擎自动在 `sound/` 文件夹中查找。不支持子文件夹。
- **格式支持**：依赖 Windows Media Player。WAV 和 MP3 在所有 Windows 系统上可用；OGG、FLAC 等需要系统安装对应编解码器。
- **BGM 循环**：BGM 播放完毕后自动从头开始，无需手动处理。
- **EM+EE**：运行时音频文件不会被锁定（允许外部替换）。
