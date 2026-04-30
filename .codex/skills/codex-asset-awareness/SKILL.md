---
name: codex-asset-awareness
description: When the user asks to create/modify/reference any character, scene, or storyboard, call get_current_session_assets() FIRST to retrieve existing assets, then reference their id+URL in the message you pass to call_character_designer / call_scene_designer / call_storyboard_artist / call_action_designer / call_screenwriter / call_sound_designer.
---

# 资产感知

`get_current_session_assets()` 是廉价的(5 秒缓存,连续调用零代价)。在每次调用 6 个 `call_*` 工具之前先用它一次,把返回的 `id` + `mainImage`/`imageUrl` 引用到给 agent 的 `message` 里,可以保住整个会话的视觉一致性。

## 错误 vs 正确

### 场景 1: 用户:「再画一个赛博朋克角色」

❌ **错误**

```
call_character_designer(message="设计一个赛博朋克角色...")
```

后果: 复制现有角色 / 风格不一致 / 浪费一次生成。

✅ **正确**

```
1. get_current_session_assets()
   → STRUCTURED 里看到 r_1 "夜城侦探 Anya" mainImage=https://...
2. call_character_designer(
       message="与 r_1 同世界观但不同阵营的黑客角色,
                参考 Anya 的服装配色 (URL: https://...) 形成阵营对比,
                色调更冷..."
   )
```

后果: 一致、连贯、形成完整人物关系。

### 场景 2: 用户:「修一下那个场景的灯光,暗一点」

❌ **错误**

```
call_scene_designer(message="把场景灯光改暗一点")
```

后果: 凭记忆 / 错改成新场景 / 主体丢失。

✅ **正确**

```
1. get_current_session_assets()
   → STRUCTURED 里看到 s_1 "Forest" imageUrl=https://...
                              descriptionShort="湿润的雨后森林..."
2. call_scene_designer(
       message="基于 s_1 (https://...) 仅修改灯光至昏暗夜色,
                保留所有树木/苔藓/雨雾元素,
                color grade 偏蓝绿..."
   )
```

后果: 灯光变化,场景骨架保留。

### 场景 3: 用户:「分镜 3 重画,这次给个特写」

❌ **错误**

```
call_storyboard_artist(message="重画分镜 3,改成特写")
```

后果: 角色身份漂移 / 跟前后镜头不衔接。

✅ **正确**

```
1. get_current_session_assets()
   → STRUCTURED 里看到 storyboards[2] order=3 imageUrl=https://...
                       涉及 r_1 (Anya) mainImage=https://...
                            r_2 (Kai)   mainImage=https://...
2. call_storyboard_artist(
       message="重画分镜 3 (现 imageUrl: https://...),
                镜头改为 r_1 (Anya: https://...) 的面部特写,
                r_2 (Kai: https://...) 在背景虚化处侧脸出现,
                保持原构图的左上→右下视线引导..."
   )
```

后果: 镜头变化但身份/构图脉络延续。

## 返回值结构

`get_current_session_assets()` 返回两段:

1. **markdown body** — 给人类(以及 LLM 自然语言阅读)读的摘要
2. **`---STRUCTURED---` 之后的 JSON** — 给 AI 引用专用,字段精简:
   - `characters[].id / name / mainImage / cref / sref / descriptionShort`
   - `scenes[].id / name / imageUrl / descriptionShort`
   - `storyboards[].id / order / imageUrl / videoUrl / descriptionShort / promptShort`

引用时优先用 STRUCTURED 段的 `id` + URL,精确无歧义。

## 兜底机制(系统层,你仍要主动查)

- **Layer 1**: 系统会在调用 5 个视觉 agent (`SCRIPT_WRITER` / `CHARACTER_DESIGNER` / `ENVIRONMENT_DESIGNER` / `STORYBOARD_DESIGNER` / `IP_DESIGNER`) 时自动注入资产摘要到 message 头。
- **Layer 4 (NEXT STEP hint)**: 每次 `call_*` 调用成功后,返回值末尾会附带 `---NEXT STEP---` 提示,指引你引用本次新增的 id+URL 到下一次调用。

但这两条都是**兜底**。你应该主动 `get_current_session_assets()` —— 它便宜(5s 缓存)、显式、无歧义,远优于完全依赖兜底。

## 可用工具

- `get_current_session_assets(workspace_id="")` — 当前 Solo 会话产出的角色/场景/分镜,含 dual-format 输出
- `get_workspace_assets(workspace_id, session_id="")` — 整个工作空间(可跨会话)的所有资产,信息更全但更长
