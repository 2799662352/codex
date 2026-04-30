# 三语提示词完整示例集

本文件包含各类场景的三语（中文+日语+英语）message 示例。SKILL.md 中的公式和规则是核心，这里是实际参考。

## 写实风格示例

### 角色设计师（①主体→③环境→④景别→⑤光影→⑥构图 ｜ JP=风格 ｜ EN=参数）

```
call_character_designer(workspace_id, "①年轻亚洲男性程序员，25岁，干净短发，黑框眼镜，浅蓝休闲衬衫，坐在工位专注敲代码（集中してコーディング／focused coding）。③现代互联网公司工位，桌上有机械键盘、咖啡杯、笔记本电脑。④半身中景。⑤柔和室内灯光，右侧落地窗自然光，光比1:2。⑥正面为主+侧面/背面全身+脸部特写，综合角色设定图。リアルな肌質感、クリーンな表情、柔らかい室内照明、商業写真のクオリティ。photorealistic, realistic human texture, real skin with visible pores, 85mm portrait lens, f/2.0, Kodak Portra 400 color tone, sharp focus")
```

### 场景设计师（①主体→③环境分层→⑤光影→⑥前中远三层构图 ｜ JP=品质 ｜ EN=参数）

```
call_scene_designer(workspace_id, "①现代互联网公司开放式办公区（モダンなオープンオフィス／modern open-plan office）。③木纹桌面+机械键盘+笔记本电脑，远处玻璃隔断+柔和办公灯带。⑤右侧落地窗自然光+室内暖色灯混合，色温5600K+3200K。⑥前景=轻微虚化的显示器边框+咖啡杯遮挡（前景ボケ／foreground bokeh），中景=工位主体，远景=同事轮廓虚化（遠景ボケ／background blur）。リアルな照明、自然素材、商業インテリア写真のクオリティ。photorealistic environment, realistic lighting, natural materials, 50mm standard lens, f/2.8, Kodak Portra 400 tone, natural color grading")
```

### 分镜师——静态分镜（①主体+动作→②角色锚点→③环境→④景别→⑤光影→⑥构图 ｜ JP=风格 ｜ EN=参数）

```
call_storyboard_artist(workspace_id, "只生成分镜静帧，不要生成视频。①程序员坐在工位前，双手在键盘上敲击，目光专注于屏幕（集中してコーディング／focused coding）。②保持与前面角色设定图中的黑框眼镜、浅蓝衬衫一致。③现代办公室工位。④中景，50mm。⑤柔和室内灯光，自然色温。⑥前景=咖啡杯虚化遮挡（前景ボケ／foreground mug bokeh），建立机位感。シネマティックスチル、リアルな人間のプロポーション、自然光。photorealistic keyframe, cinematic still, real human proportions, 50mm standard lens, f/2.8, natural lighting, Kodak Portra 400 tone, film grain")
```

### 分镜师——视频（①主体+动作→②角色锚点→④镜头运动→⑤光影→⑥构图 ｜ JP=动态风格 ｜ EN=运镜参数）

```
call_storyboard_artist(workspace_id, "生成第1段视频(5秒)，使用拆分后的第1张分镜图作为首帧参考。①程序员缓慢抬手推眼镜（メガネを直す／adjusting glasses），眼神保持注视屏幕。②保持角色设定图中的外观一致。④Slow dolly in 从中景推进到中近景。⑤自然室内光线。⑥前景=桌面咖啡杯蒸汽微微飘动（湯気がゆっくり立ち上る／subtle steam rising）。スローなドリーイン、繊細な動き。slow dolly in, subtle movement, natural indoor lighting, no abrupt camera motion. Negative: blurry, deformed, bad anatomy, extra limbs, watermark")
```

## 多角色示例（①主体→②角色锚点→③环境→④景别→⑤光影→⑥构图 ｜ JP=风格 ｜ EN=参数）

```
call_character_designer(workspace_id, "①四位程序员围绕会议桌讨论项目实施方案（プロジェクト実施計画ミーティング／project implementation planning meeting）；锚点=一人：黑发高马尾・細縁メガネの女性（thin-frame glasses），动作=一人指向屏幕（指さし／pointing），一人笔电演示（デモ／demo），一人手写记录（メモを取る／taking notes）。②各人服装颜色/发型/标记物需互不相同以便后续辨识。③现代开放式办公室（モダンなオープンオフィス／modern open-plan office），大屏显示微服务架构图。④35mm広角ミディアムショット。⑤日光+暖色台灯混合，5600K+3200K，光比1:3，侧逆光リムライト。⑥前景=玻璃反射+咖啡杯虚化（前景ボケ／foreground bokeh）；状态快照=确认里程碑与分工的瞬间。リアルな肌質感、商業写真のクオリティ。photorealistic, realistic human texture, 35mm wide lens, f/2.8, 1/125s, ISO 400, Kodak Portra 400 tone")
```

## 动漫风格示例（①主体+动作→③背景→⑤光影→⑥构图 ｜ JP=动画风格 ｜ EN=动画参数）

当用户指定动漫/二次元风格时，日语比重提高，使用动画专业术语：

```
call_character_designer(workspace_id, "①古风女剑客，长发飘逸，右手持太刀斜举（刀身与肩线呈45°），左手轻按刀鞘。③竹林月夜，月光从左上方穿过竹叶缝隙。⑤逆光月影，主体面部侧光，光比1:4。⑥前景=竹叶虚化遮挡，中景=剑客全身，远景=雾气弥漫的竹林深处。日本製2Dアニメーション、劇場版レベルの画面、さわやかな明暗のコントラスト、光と影の表現。anime screencap, TV anime coloring, modern anime style, cel shading, cinematic lighting, depth of field, key animation frames, masterpiece, best quality")
```

## 三语关键原则

1. **三语并存不可省略**——每个 message 必须同时包含中文、日语、英语三段
2. **中文写内容、日语写风格、英语写参数**——各取所长
3. **内联标注强化关键概念**——在中文叙述中用括号`（日語／English）`标注关键术语
4. **风格词用日语和英语双写**——如`リアルな肌質感 / realistic skin texture`
5. **相机参数用英语**——焦距、光圈、ISO、色调等技术参数始终用英语
