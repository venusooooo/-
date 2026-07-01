# 关卡大师 - 项目进度文档

> 最后更新：2026-06-30
> 当前版本：v0.8

## 项目一句话定位

覆盖所有游戏类型的双向专家 Agent——既做通关策略教练，也做游戏机制设计顾问；严谨教练风格，精度到关卡，不飘。

---

## 当前版本状态（v0.8）

### 已完成

1. `SOUL.md` v0.4 —— 无变更
2. `IDENTITY.md` v0.2 —— 无变更
3. `AGENTS.md` v0.7 —— **新增流程 G「框架完成后直接出游戏」**：框架/配置确认后主动询问→用户确认→立即生成；含2D/3D选型逻辑、运行方式告知、不主动问的例外情况；示例Skill表新增 p5js + threejs 两行
4. `USER.md` v0.2 —— 无变更
5. `HEARTBEAT.md` v0.1 —— 无变更
6. `TOOLS.md` v0.4 —— 示例Skills节新增 p5js-creative-coding 和 threejs 两个条目，强调「直接生成可运行游戏HTML」
7. `BOOT.md` v0.1 —— 无变更
8. `BOOTSTRAP.md` v0.1 —— 无变更
9. `docs/README.md` v0.1 —— 无变更
10. `MEMORY.md` v0.1 —— 无变更
11. `memory/_TEMPLATE.md` v0.1 —— 无变更
12. `skills/baidu-ai-search/SKILL.md` v0.2 —— 无变更
13. `skills/image-understanding/SKILL.md` v0.2 —— 无变更
14. `skills/text-translation/SKILL.md` v0.2 —— 无变更
15. `skills/image-upload/SKILL.md` v0.1 —— 无变更
16. `skills/minimax-m3/SKILL.md` v0.1 —— 无变更
17. `skills/kling-image-gen/SKILL.md` v0.1 —— 无变更
18. `skills/image-generation-super/SKILL.md` v0.1 —— 无变更
19. `skills/p5js-creative-coding/SKILL.md` v0.1（**新建**）—— 示例Skill：直接生成2D游戏HTML；含5类游戏模板（扫雷/大鱼/塔防/PvZ/消消乐）、核心循环代码结构、与流程G的对接说明
20. `skills/threejs/SKILL.md` v0.1（**新建**）—— 示例Skill：直接生成3D游戏HTML；含标准场景初始化模板（PBR+阴影+ACESFilmic）、与p5js的选型对比、3D质量要求
21. `avatar.png` —— 无变更

### v0.8 关键决策

| 决策 | 原因 |
|------|------|
| 新增主动询问行为（流程G） | 用户需要框架搭好后Agent能主动推进到生成，而不是等用户来问 |
| p5js 用于 2D，three.js 用于 3D | 明确的选型分工：p5js 轻量快速适合2D原型，three.js 处理3D光影和PBR效果 |
| 配置更改后同样触发询问 | 迭代修改是常态场景，每次配置改完都应能直接重新生成 |

### 已完成

1. `SOUL.md` v0.4 —— **新增原则 6「画质不凑合」**：5个视觉维度（分辨率/光影/粒子动效/美术规格一致性/色彩体系），要求给具体问题不给模糊评价
2. `IDENTITY.md` v0.2 —— 无变更
3. `AGENTS.md` v0.6 —— **新增流程 F：游戏视觉质量评审**（5步：定位问题层→具体结论→可操作下一步→视觉生成辅助→不替用户判断）；示例 Skill 表新增 `image-generation-super` 条目
4. `USER.md` v0.2 —— 无变更
5. `HEARTBEAT.md` v0.1 —— 无变更
6. `TOOLS.md` v0.3 —— 示例 Skills 节新增 `image-generation-super`；工具协作原则补充视觉质量提升路径说明
7. `BOOT.md` v0.1 —— 无变更
8. `BOOTSTRAP.md` v0.1 —— 无变更
9. `docs/README.md` v0.1 —— 无变更
10. `MEMORY.md` v0.1 —— 无变更
11. `memory/_TEMPLATE.md` v0.1 —— 无变更
12. `skills/baidu-ai-search/SKILL.md` v0.2 —— 无变更
13. `skills/image-understanding/SKILL.md` v0.2 —— 无变更
14. `skills/text-translation/SKILL.md` v0.2 —— 无变更
15. `skills/image-upload/SKILL.md` v0.1 —— 无变更
16. `skills/minimax-m3/SKILL.md` v0.1 —— 无变更
17. `skills/kling-image-gen/SKILL.md` v0.1 —— 无变更（描述更新为"快速出图"以区分定位）
18. `skills/image-generation-super/SKILL.md` v0.1（**新建**）—— 旗舰级图像生成示例 Skill；GPT-Image-2，支持文生图+多图编辑融合；含游戏各类型专用 Prompt 模板、五维视觉质量标准、与 kling-image-gen 的选型对比表
19. `avatar.png` —— 无变更

### v0.7 关键决策

| 决策 | 原因 |
|------|------|
| 新增 SOUL.md 原则 6「画质不凑合」| 用户反馈游戏画质简陋，需要 Agent 在认知层就具备视觉质量标准，而非仅在工具层响应 |
| AGENTS.md 新增流程 F | 视觉质量问题有固定的诊断路径（5层），结构化流程比临时回答更可靠 |
| 选用 image-generation-super 而非仅扩展 kling | GPT-Image-2 支持多图编辑融合，解决风格不一致问题；这是 kling 不具备的能力 |

### 已完成

1. `SOUL.md` v0.3 —— 无变更
2. `IDENTITY.md` v0.2 —— 无变更
3. `AGENTS.md` v0.5 —— Skill 调用规范节新增示例 Skills 汇总表（image-upload / minimax-m3 / kling-image-gen 的激活条件说明）
4. `USER.md` v0.2 —— 无变更
5. `HEARTBEAT.md` v0.1 —— 无变更
6. `TOOLS.md` v0.2 —— 专业 Skills 节拆分为"已激活"和"示例 Skills（按需激活）"两段，新增三个示例 skill 的一行描述和路径引用
7. `BOOT.md` v0.1 —— 无变更
8. `BOOTSTRAP.md` v0.1 —— 无变更
9. `docs/README.md` v0.1 —— 无变更
10. `MEMORY.md` v0.1 —— 无变更
11. `memory/_TEMPLATE.md` v0.1 —— 无变更
12. `skills/baidu-ai-search/SKILL.md` v0.2 —— 无变更
13. `skills/image-understanding/SKILL.md` v0.2 —— 无变更
14. `skills/text-translation/SKILL.md` v0.2 —— 无变更
15. `skills/image-upload/SKILL.md` v0.1（新建）—— 示例 Skill：游戏素材上传，含与 image-understanding 的组合流程和激活说明
16. `skills/minimax-m3/SKILL.md` v0.1（新建）—— 示例 Skill：MiniMax-M3 多模态深度分析，含代码架构分析/设计文档理解/图文混合输入场景
17. `skills/kling-image-gen/SKILL.md` v0.1（新建）—— 示例 Skill：可灵图像生成，含概念图/UI参考图/关卡意境图生成，标注按次计费和版权约束
18. `avatar.png` —— 无变更

### 已完成

1. `SOUL.md` v0.3 —— 无变更
2. `IDENTITY.md` v0.2 —— 无变更
3. `AGENTS.md` v0.4 —— 无变更
4. `USER.md` v0.2 —— 无变更
5. `HEARTBEAT.md` v0.1 —— 无变更
6. `TOOLS.md` v0.1（新建）—— 三个 skill 完整配置 + 本地脚本预留 + 工具协作原则
7. `BOOT.md` v0.1（新建）—— 启动自检清单：环境/状态/一致性三项检查 + 失败处理逻辑（自动修复可恢复项）
8. `BOOTSTRAP.md` v0.1（新建）—— 首次部署引导：6步初始化（身份/人设/用户档案/工具就绪/记忆初始化/完成清理），**首次部署完成后删除本文件**
9. `docs/README.md` v0.1（新建）—— 参考文档目录，含4份建议文档清单（游戏机制拆解/引擎速查/关卡设计模式/实现模式）
10. `MEMORY.md` v0.1 —— 无变更
11. `memory/_TEMPLATE.md` v0.1 —— 无变更
12. `skills/baidu-ai-search/SKILL.md` v0.2 —— 无变更
13. `skills/image-understanding/SKILL.md` v0.2 —— 无变更
14. `skills/text-translation/SKILL.md` v0.2 —— 无变更
15. `avatar.png` —— 无变更

---

## 关键决策记录（v0.3 新增）

| 日期 | 决策 | 原因 | 替代方案 |
|------|------|------|----------|
| 2026-06-30 | MEMORY.md 放 Agent 根目录，daily 放 memory/ 子目录 | 两类文件职责不同：MEMORY.md 是全局长期状态（每次必读），daily 是会话流水（只读最新一条）；分开存放读取路径更清晰 | 全部放 memory/ 目录下 |
| 2026-06-30 | MEMORY.md 增加"已记录的游戏知识"第三段 | 游戏专家的核心价值是积累跨会话的关卡分析结论；原模板只有偏好+摘要，缺少知识沉淀段 | 只用用户原始两段结构 |
| 2026-06-30 | daily 模板新增 Skill 调用记录字段 | 三个 skill 调用效果需要追踪（image-understanding 识别质量差异 / baidu-ai-search 是否查到有效资料），原模板无此字段 | 不记录 skill 调用，只记结论 |
| 2026-06-30 | daily frontmatter 加 mode 字段（攻略/开发/机制分析/推荐） | 会话类型影响后续检索效率；日后批量回顾"开发类会话"时可快速过滤 | 只记 game + level |



| 日期 | 决策 | 原因 | 替代方案 |
|------|------|------|----------|
| 2026-06-30 | 集成 baidu-ai-search、image-understanding、text-translation 三个 skill | 三者覆盖游戏专家最高频痛点：数据时效性、截图分析、外文内容理解；互相不重叠 | 加 wenxin-text-generation（与 Agent 自身能力重叠，意义不大）|
| 2026-06-30 | 不集成文生图 skill | 游戏专家是分析/教练角色，不需要生成图片；加了反而模糊定位 | minimax-text-to-image |
| 2026-06-30 | AGENTS.md 在 Red Lines 前增加独立"Skill 调用规范"章节 | 三个 skill 各有不同约束（question字符限制/轮询等待/术语附注），单独成章更清晰 | 分散写入各场景工作流 |

## 关键决策记录（v0.1）

| 日期 | 决策 | 原因 | 替代方案 |
|------|------|------|----------|
| 2026-06-30 | Agent 名定为"关卡大师"而非"游戏专家" | "关卡"比"游戏"更精准——强调拆解分析而非泛娱乐；"大师"对应严谨教练定位 | 游戏教练、关卡分析师 |
| 2026-06-30 | 双定位（攻略+开发）合并进同一 Agent | 用户明确要求两者都要；两者底层是同一件事（游戏系统分析），拆成两个 Agent 反而割裂 | 拆成"攻略版"和"开发版"两个 Agent |
| 2026-06-30 | 覆盖所有游戏类型，不限定具体品类 | 用户要求"所有类型都覆盖"；通用分析框架比品类专精更有扩展性 | 只覆盖扫雷/塔防/PvZ等指定品类 |
| 2026-06-30 | 严谨教练风格，不用老玩家/主播风 | 用户选择"分析精准、结构清晰"；与双定位（玩家+开发者）都契合 | 老玩家口语风、主播激情风 |
| 2026-06-30 | HEARTBEAT 全注释，无定时任务 | 用户选择"被动对话即可"，无推送需求 | 启用每日 tip 推送 |

---

## 待验证（部署后才知道）

- Qclaw HEARTBEAT 触发精度（到分钟 or 到小时）——本 Agent 暂时不需要，但日后启用时需记录
- `./avatar.png` 相对路径在 Qclaw runtime 是否正确读取
- Vibe 字段内容在 Qclaw 控制台的显示截断长度
- 多轮对话中 USER.md 的写入时机（Onboarding 触发条件是否精准）
