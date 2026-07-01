# TOOLS.md - 关卡大师

## 专业 Skills

### 已激活（正式集成）

- `baidu-ai-search`：实时搜索引擎官方文档、游戏开发技术文章、算法实现参考、同类游戏设计分析；解决训练数据时效性问题，查最新版本API和开发社区讨论
- `image-understanding`：识别用户上传的参考游戏截图，从开发者视角分析UI布局结构、关卡地形分布、视觉风格要素；也可分析用户上传的手绘设计草图
- `text-translation`：翻译外文引擎文档（Unity/Godot英文文档）、技术博客、GDC演讲资料、代码注释；技术专有名词附原文保留

详见各 skill 的调用规范：
- `skills/baidu-ai-search/SKILL.md`
- `skills/image-understanding/SKILL.md`
- `skills/text-translation/SKILL.md`

### 示例 Skills（按需激活）

以下为扩展能力示例，已在 `skills/` 目录下预备 SKILL.md，需配置后正式启用：

- `image-upload`：上传游戏截图/素材/手绘草图到云端，配合 `image-understanding` 联合使用
  → `skills/image-upload/SKILL.md`
- `minimax-m3`：MiniMax-M3 多模态大模型，深度分析游戏代码架构、设计文档，支持1M超长上下文和图文混合输入
  → `skills/minimax-m3/SKILL.md`
- `kling-image-gen`：可灵图像生成，根据描述快速生成游戏概念图、UI风格参考图、关卡意境图（按次计费约8元/次）
  → `skills/kling-image-gen/SKILL.md`
- `image-generation-super`：GPT-Image-2 旗舰级图像生成与编辑，画质上限高于可灵；支持多图编辑融合、风格统一、局部重绘；适合精品美术资产和发布级素材生产（使用 INTEGRATIONS_API_KEY，平台托管）
  → `skills/image-generation-super/SKILL.md`
- `p5js-creative-coding`：**直接生成可运行 2D 游戏 HTML**（扫雷/大鱼吃小鱼/2D塔防/PvZ/消消乐等）；框架/配置确认后关卡大师主动询问并立即生成，打开即玩
  → `skills/p5js-creative-coding/SKILL.md`
- `threejs`：**直接生成可运行 3D 游戏 HTML**（3D塔防/FPS/TPS/3D跑酷等）；带 PBR 材质、阴影、粒子特效；用户要 3D 效果时优先选此 Skill
  → `skills/threejs/SKILL.md`

---

## 本地脚本

当前版本暂无本地脚本。如日后扩展以下能力，在此目录添加：

- 公共工具库：`lib/common.py`（预留）
- 关卡生成脚本：`skills/level-gen/scripts/generate.py`（预留）

Python 脚本建议 Python 3.10 或更高版本，仅依赖标准库。

---

## 通用能力协作

- **读取引擎文档/PDF**：调用环境已有 PDF/文档 Skill，处理官方文档附件
- **网络技术研究**：优先调用 `baidu-ai-search`，查官方文档和权威技术博客
- **外文资料理解**：调用 `text-translation` + `baidu-ai-search` 组合
- **参考截图分析**：调用 `image-understanding`，问题（question）控制在 100 字以内

---

## 工具协作原则

`TOOLS.md` 只记录工具约定，不赋予工具权限。实际可用性由运行环境配置决定。

工具调用前置规则：
- 调用任何 skill 前，告知用户正在调用（不静默调用）
- skill 结果只作为输入原料，关卡大师的分析判断是主体
- 示例 skill 可组合使用（如：先翻译外文截图文字，再分析截图内容；先生成概念图，再分析是否达标）
- 视觉质量提升路径：快速验证 → `kling-image-gen`；精品输出/多图融合 → `image-generation-super`；效果评估 → `image-understanding`
