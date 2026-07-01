# BOOT.md - 关卡大师

启动自检清单。每次会话启动时按顺序检查。

---

## 环境检查

- [ ] `skills/baidu-ai-search/SKILL.md` 可读，skill 可用
- [ ] `skills/image-understanding/SKILL.md` 可读，skill 可用
- [ ] `skills/text-translation/SKILL.md` 可读，skill 可用
- [ ] `memory/` 目录可读写（用于写入会话日志）
- [ ] `docs/` 目录可读（用于查阅游戏开发参考资料）

---

## 状态检查

- [ ] 读取 `MEMORY.md`，加载长期偏好 + 近期任务摘要 + 已记录游戏开发知识
- [ ] 读取 `memory/` 下最新一条 daily 日志，恢复上次对话上下文
- [ ] 检查 `HEARTBEAT.md`，确认是否有已注释之外的激活任务（默认全注释，无激活）
- [ ] 读取 `USER.md`，确认用户当前开发项目状态

---

## 一致性检查

- [ ] `IDENTITY.md` 的 Vibe 与 `SOUL.md` 定位一致（游戏制作/开发专家方向）
- [ ] `TOOLS.md` 引用的三个 skill 在 `skills/` 目录下均有对应 `SKILL.md`
- [ ] `memory/_TEMPLATE.md` 存在，用于新建 daily 日志
- [ ] `AGENTS.md` 中 Session Startup 读取顺序与当前文件结构匹配

---

## 失败处理

某项检查失败时：

1. 记录失败项到 `memory/` 当日日志（标注 `[BOOT_FAIL]` 前缀）
2. **不阻塞会话**——降级运行，告知用户缺失的能力（如"今天截图分析 skill 不可用"）
3. 可自动修复项：
   - `memory/` 目录不存在 → 自动创建 `memory/`、`memory/weekly/`、`memory/archive/`
   - `memory/_TEMPLATE.md` 缺失 → 提示用户，但不阻塞
4. 无法自动修复项（如 skill 不可用）→ 明确告知，不静默跳过
