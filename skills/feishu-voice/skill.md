---
name: send-voice-to-feishu
description: 飞书语音消息发送。将文字转为语音条发送到飞书。
trigger: 系统消息包含"Feishu"或"飞书"，且满足以下任一条件时必须使用：(1) 用户发送语音消息（含[Audio]或file_key）→ 必须用语音回复；(2) 用户要求语音回复。飞书渠道禁止使用tts工具。
---

## 核心规则

**飞书渠道语音回复规则**：
| 场景 | 动作 |
|------|------|
| 收到语音消息 | **必须**运行脚本语音回复 |
| 用户要求语音回复 | 运行脚本语音回复 |
| 用户说"文字回复" | 可以用文字回复 |

**禁止**：飞书渠道使用 `tts` 工具（飞书不支持 tts 生成的音频格式）

## 使用方法

运行 `feishu_voice.py` 脚本：

```bash
python "{{skill_path}}/feishu_voice.py" "要发送的文字" --voice "zh-CN-XiaoxiaoNeural"
python "{{skill_path}}/feishu_voice.py" "要发送的文字" --agent-id "main" --user "ou_xxx"
python "{{skill_path}}/feishu_voice.py" "要发送的文字" -a "worker1" -u "ou_xxx" --voice "zh-CN-XiaoyiNeural"
```

| 参数 | 必需 | 说明 |
|------|------|------|
| 第一个参数 | ✅ | 要转为语音的文字 |
| --agent-id, -a | 可选 | Agent ID，用于根据bindings找到对应的飞书账号和音色配置，默认 `main` |
| --user, -u | 可选 | 发送目标ID，支持两种格式：<br>- `ou_xxx` 用户 open_id（私聊）<br>- `oc_xxx` 群聊 chat_id（群聊），自动识别 |
| --voice, -v | 不要传入这个参数，除非用户要求传递。因为每个agent已经配置了银音色 | 音色代码，覆盖配置中默认音色 |

## 发送目标说明

- **用户私聊**：使用用户的 `open_id`，格式以 `ou_` 开头
- **群聊发送**：使用群聊的 `chat_id`，格式以 `oc_` 开头
- 程序会自动根据前缀判断类型，无需额外参数

注意：
- 因为用户在每一个飞书应用机器人那里都有一个单独的open_id，所以要确保配置正确。
- 如果 bindings 配置的是 `worker1` 对应 feishu:liqingzhao，参数 `--user` 只能配置用户/群组在 liqingzhao 这里的ID，否则就会遇到跨应用发送消息的报错。

## 示例

```bash
python "{{skill_path}}/feishu_voice.py" "你好，我是李清照" -a "worker1" -u "ou_4cf7abab9361b11bdfa4d9c460392c05" --voice "zh-CN-XiaoyiNeural"
python "{{skill_path}}/feishu_voice.py" "你好，我是王二锤" -a "main" -u "ou_cfdab43b1497d525818dec568c870a10"
python "{{skill_path}}/feishu_voice.py" "你好，我是墨子" -a "worker3" -u "ou_8f093b0e9274c8ff37b312e05ec37872"
python "{{skill_path}}/feishu_voice.py" "你好，我是小蜜蜂" -a "eukefu" -u "oc_f8348e21de0ea6bbc9bdc83ff296f770"
```

## 常用音色
### Edge TTS
| 音色代码 | 特点 |
|---------|------|
| zh-CN-XiaoxiaoNeural | 女声，温柔自然 |
| zh-CN-XiaoyiNeural | 女声，年轻活泼 |
| zh-CN-YunjianNeural | 男声，沉稳有力 |
| zh-CN-YunxiNeural | 男声，阳光开朗 |
| zh-CN-YunyangNeural | 男声，新闻播报风格 |
| zh-CN-liaoning-XiaobeiNeural | 女声，东北口音 |
| zh-CN-shaanxi-XiaoniNeural | 女声，陕西口音 |

完整音色列表见 [voice-handle 技能](../voice-handle/SKILL.md)。

## 注意事项

- 仅用于飞书渠道
- 自动将 MP3 转为 OPUS 格式
- 需要 FFmpeg 已安装
