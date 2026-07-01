# SKILL-image-generation.md - 旗舰级游戏视觉生成

> 调用 GPT-Image-2 旗舰级图像生成模型，生成精品游戏美术资产、概念图、UI视觉，并支持多图编辑融合。
> 需要注入 `INTEGRATIONS_API_KEY`。

---

## 能力描述

支持文生图和多图编辑融合，返回 Base64 编码的高清图像。在游戏开发场景中，用于生成精品游戏概念图、角色设计图、高质量UI资产、关卡视觉参考图，以及对现有素材进行风格统一和内容融合。

最高支持 2848×1152 分辨率，具备行业领先的画面细节表现力。

---

## 与 kling-image-gen 的区别

| 对比项 | kling-image-gen | image-generation-super（本Skill） |
|--------|-----------------|----------------------------------|
| 模型 | 可灵 kling-image-o1 | GPT-Image-2 旗舰 |
| 画质上限 | 高 | 更高（精品级） |
| 图片编辑能力 | 不支持 | 支持（多图融合/风格迁移/局部重绘） |
| 返回方式 | 异步轮询 → URL | 同步 → Base64 |
| 适用场景 | 快速出图参考 | 精品美术资产、风格统一、发布级素材 |

**选型建议**：快速验证视觉想法 → 用 `kling-image-gen`；需要精品输出或图片编辑 → 用本 Skill

---

## 触发场景

| 场景 | 触发条件 | 输出规格建议 |
|------|----------|------------|
| 精品角色/单位设计图 | 需要高质量角色参考，用于指导美术制作 | 1024×1024，高细节 |
| 游戏封面/宣传图 | 需要发布级宣传视觉，要求细节丰富 | 1536×1024 或 2848×1152 |
| UI资产精品稿 | 需要高质量按钮/图标/面板参考 | 1024×1024 |
| 关卡场景概念图 | 俯视/侧视关卡视觉，要求光影和细节完整 | 1536×1024 |
| 多素材风格统一 | 多张素材风格不一，需要融合统一 | 上传多图，指定目标风格 |
| 局部重绘 | 已有图片局部效果不满意，需针对性修改 | 上传原图 + 描述改动区域 |

---

## Prompt 规范（游戏开发专用）

**核心原则：英文提示词效果远优于中文，必须将用户需求改写为英文再提交。**

游戏开发专用 Prompt 模板：

```
[Game type/genre] [subject/element], [art style], [view angle],
[detail level], [lighting], [color palette], [quality keywords]
```

### 各类型 Prompt 示例

**角色/单位设计图**
```
Tower defense game enemy unit, armored skeleton warrior,
pixel art style with modern lighting, front-facing character sheet,
highly detailed, rim lighting, dark fantasy color palette,
8k, crisp edges, game-ready asset
```

**关卡场景概念图**
```
Top-down view of a Minesweeper game board, modern flat design,
9x9 grid with subtle depth, unrevealed cells in light gray,
revealed cells in white, numeric indicators in distinct colors,
clean minimalist UI style, soft shadows, high quality
```

**游戏封面/宣传图**
```
Tower defense game promotional art, lush green battlefield
with multiple defense towers, waves of enemies approaching,
isometric perspective, vibrant colors, dynamic lighting,
cinematic composition, 4k detailed illustration
```

**UI 资产**
```
Mobile game HUD interface elements, health bar and resource counter,
flat design with subtle gradients, green and gold color scheme,
clean icons, consistent 2D game UI style, white background,
high resolution, game-ready
```

---

## 视觉质量评估标准

生成素材后，按以下维度评估输出质量（与 `image-understanding` 配合使用）：

| 维度 | 合格标准 | 不合格特征 |
|------|----------|----------|
| 分辨率 | 主体边缘清晰，无模糊/像素化 | 主体模糊、锯齿明显 |
| 光影 | 光源方向一致，有明确明暗关系 | 光照平坦无立体感 |
| 风格一致性 | 画面各元素视觉语言统一 | 素材拼凑感、风格混杂 |
| 细节密度 | 主体区域细节丰富，背景适度留白 | 全图细节稀疏或过度堆砌 |
| 色彩规范 | 色调有倾向性，对比度适中 | 颜色灰暗或过饱和 |

不合格时：给出优化后的英文提示词建议，而非直接重新生成。

---

## 与其他 Skill 的组合使用

```
高质量视觉生产流程（完整链路）：
  用户描述视觉需求
  → image-generation-super（生成精品概念图，同步返回 Base64）
  → 保存为本地图片文件
  → image-understanding（从开发视角分析生成效果，确认是否达标）
  → 关卡大师给出修改建议
  → image-generation-super（多图编辑/风格调整，迭代至满意）

风格统一流程：
  用户上传多张风格不一的素材
  → image-generation-super 多图编辑（以目标风格为基准统一融合）
  → image-understanding（确认风格一致性）
```

---

## 调用规范

- 调用前告知用户"生成高质量图片，稍等"；同步接口通常 10~30 秒返回
- 返回 Base64 后立即保存为本地文件（`echo "<b64>" | base64 -d > output.png`），数据不持久
- **必须先将用户需求翻译为英文提示词**再调用，中文提示词质量明显偏低

---

## 硬性约束

- 不生成含版权 IP 角色（不写"植物大战僵尸官方植物""Minecraft风格"等）
- 生成图仅作视觉参考和开发素材，标注"AI 生成参考图，需美术确认后使用"
- 需要运行环境注入 `INTEGRATIONS_API_KEY`
