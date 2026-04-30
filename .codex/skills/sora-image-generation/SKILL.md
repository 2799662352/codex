---
name: sora-image-generation
description: 图片生成的模型选择、尺寸、画质指南。当调用 call_character_designer、call_scene_designer、call_storyboard_artist、call_action_designer 且 message 涉及生图（角色主图、概念图、场景图、分镜静帧）时触发。指导如何在 message 中指定 model / size / quality 以获得最佳性价比。
---

# 图片生成模型指南

## 可用模型（按优先级）

| 模型 | 后端 | 速度 | 成本 | 适用场景 |
|------|------|------|------|----------|
| `gemini-3.1-flash-image-preview` | Gemini | ⚡ 最快（15-40s） | 💰 最低 | **默认首选**。草图、概念图、角色初版、批量分镜、快速迭代 |
| `gpt-image-2-all` | OpenAI (逆向) | 🔵 中等（30-60s） | 💰💰 中等 | 常用。质量好，适合需要精细画面、角色终稿、场景定稿 |
| `gpt-image-2` | OpenAI (官转) | 🐢 最慢（60-120s） | 💰💰💰 最高 | 终稿级。仅用于最终交付物、高清海报、客户展示 |

**不传 model 时默认使用 gemini-3.1-flash-image-preview。**

## 选择决策树

```
需要生成图片？
  ├─ 快速迭代/草图/批量 → gemini-3.1-flash-image-preview
  ├─ 日常定稿/精细画面 → gpt-image-2-all
  └─ 终稿/超高质量     → gpt-image-2
```

## 尺寸选项

| 尺寸 | 比例 | 用途 |
|------|------|------|
| `1024x1024` | 1:1 | **默认**。角色主图、头像、概念图 |
| `1536x1024` | 3:2 | 横版场景、分镜画面 |
| `1024x1536` | 2:3 | 竖版角色全身、海报 |
| `2048x2048` | 1:1 | 高清终稿（仅 gpt-image-2 系列） |
| `auto` | 自动 | 由模型自行决定 |

**Gemini 后端忽略 size 参数**，始终返回模型默认尺寸。需要精确控制尺寸时用 gpt-image-2 系列。

## 画质档位

| 档位 | 成本（gpt-image-2 基准） | 用途 |
|------|------------------------|------|
| `low` | ~$0.006/张 | 草图、批量生成、快速预览 |
| `medium` | ~$0.053/张 | **日常默认**（generate_image 默认值） |
| `high` | ~$0.211/张 | 终稿级高质量 |
| `auto` | 服务端决定 | 不确定时 |

**generate_image_nano 默认 quality=low，generate_image 默认 quality=medium。**

## 在 message 中指定模型

视觉 agent 的 `generate_image_nano` 和 `generate_image` 工具接受 `model` 参数。你在传给 `call_*` 工具的 `message` 中可以明确要求使用特定模型：

### 示例

快速草图（默认 gemini）：
```
call_character_designer(workspace_id, "设计一个赛博朋克女性角色，短发，机械臂...")
```
agent 内部默认使用 gemini-3.1-flash-image-preview。

指定高质量模型：
```
call_character_designer(workspace_id, "设计一个赛博朋克女性角色，短发，机械臂... 使用 gpt-image-2-all 模型生成高质量角色图")
```

终稿级海报：
```
call_scene_designer(workspace_id, "为赛博朋克场景生成终稿级高清海报，2048x2048 尺寸，使用 gpt-image-2 模型，quality=high")
```

## 模型能力差异

| 能力 | gemini | gpt-image-2-all | gpt-image-2 |
|------|--------|----------------|-------------|
| 文生图 | ✅ | ✅ | ✅ |
| 参考图编辑 | ✅ | ✅ | ✅ |
| 精确尺寸控制 | ❌ | ✅ | ✅ |
| quality 参数 | ❌ | ❌ | ✅ |
| 超高清 2048+ | ❌ | ❌ | ✅ |
| 中文 prompt | ⚠️ 一般 | ✅ 好 | ✅ 好 |

## 批量生成策略

分镜静帧等批量场景（>3张图）：
1. 全部用 `gemini-3.1-flash-image-preview` + `quality=low` 快速出草稿
2. 选出需要精修的关键帧，用 `gpt-image-2-all` + `quality=medium` 重绘
3. 最终展示帧用 `gpt-image-2` + `quality=high` + `size=2048x2048`
