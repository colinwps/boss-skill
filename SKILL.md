---
name: create-boss
description: Distill your boss into an AI Skill. Understand their decision-making, communication preferences, red lines, and approval patterns. | 把老板蒸馏成 AI Skill，读懂他的决策逻辑、沟通偏好、雷区和拍板规律，让你每次汇报都稳了。
argument-hint: [boss-name-or-slug]
version: 1.0.0
user-invocable: true
allowed-tools: Read, Write, Edit, Bash
---

> **Language / 语言**: 根据用户第一条消息的语言，全程使用同一语言。

# 老板.skill 创建器（Claude Code 版）

## 触发条件

当用户说以下任意内容时启动：

- `/create-boss`
- "帮我创建一个老板 skill"
- "我想蒸馏我的老板"
- "新建老板"
- "给我做一个 XX 老板的 skill"

当用户对已有老板 Skill 追加材料时，进入进化模式：
- "我有新文件" / "追加"
- "他不是这样的" / "他更喜欢"
- `/update-boss {slug}`

当用户说 `/list-bosses` 时列出所有已生成的老板 Skill。

---

## 主流程：创建新老板 Skill

### Step 1：基础信息录入（4 个问题）

只问 4 个问题，其余凭推断填充：

1. **花名/代号**（必填）
   - 示例：`老王`、`CTO`、`L总`

2. **基本信息**（一句话）
   - 格式：公司、职位、管理风格关键词
   - 示例：`字节产品副总裁，管 200 人，OKR 信仰者`
   - 示例：`创业公司 CTO，前阿里 P9，技术细节控`

3. **你和他的关系**（一句话）
   - 示例：`直属下属，周报汇报，每周 1:1`
   - 示例：`隔层汇报，季度述职，平时几乎不接触`

4. **你对他的核心印象**（1-3 个标签）
   - 示例：`PPT 控 · 数据驱动 · 会议前必须对齐`
   - 示例：`话少但有话必中 · 最怕没有结论 · 特别在意竞品`

除花名外均可跳过。收集完毕后汇总确认再进入下一步。

---

### Step 2：原材料导入

```
原材料怎么提供？

  [A] 飞书/钉钉自动采集（推荐）
      拉取他发过的文档、会议纪要、群消息

  [B] 上传文件
      周报邮件 / 会议录音文字稿 / 他写的文章截图

  [C] 直接粘贴
      把和他的对话记录、他的原话复制进来

  [D] 跳过
      仅凭你的描述生成（也能用，但精度低一些）

原材料质量决定 Skill 质量。他主动写的长文 > 他的决策类回复 > 日常消息。
```

---

### Step 3：分析原材料

将所有原材料和用户填写的信息，按两条线分析：

**线路 A（老板工作风格 — Boss Work Style）**：
参考 `${CLAUDE_SKILL_DIR}/prompts/boss_work_analyzer.md`

提取维度：
- 决策逻辑（数据驱动 / 直觉判断 / 共识优先）
- 汇报偏好（结论前置 / 过程展示 / PPT 还是口头）
- 拍板规律（什么情况一票否决 / 什么情况放权）
- 关注焦点（商业结果 / 技术深度 / 团队氛围 / 对外形象）
- 时间偏好（喜欢提前对齐 / 接受临时汇报 / 响应速度要求）
- 已知雷区（不能触碰的话题、竞品、项目）

**线路 B（老板人格画像 — Boss Persona）**：
参考 `${CLAUDE_SKILL_DIR}/prompts/boss_persona_builder.md`

提取 5 层：
- Layer 0：硬规则（他的绝对红线，违反就没好果子）
- Layer 1：身份与自我认知（他认为自己是什么样的 leader）
- Layer 2：表达风格（他怎么说话、喜欢什么风格的汇报）
- Layer 3：决策模式（他在什么情况下会拍板、什么情况会推回）
- Layer 4：人际行为（他怎么对待下属、跨部门协作、向上汇报）

---

### Step 4：生成并预览

向用户展示摘要，询问确认：

```
老板工作风格摘要：
  - 决策风格：{xxx}
  - 汇报偏好：{xxx}
  - 关注焦点：{xxx}
  - 已知雷区：{xxx}
  ...

老板人格摘要：
  - 核心人设：{xxx}
  - 表达偏好：{xxx}
  - 拍板规律：{xxx}
  ...

确认生成？还是需要调整？
```

---

### Step 5：写入文件

用户确认后，写入以下文件：

**目录结构**（Bash）：
```bash
mkdir -p bosses/{slug}/versions
mkdir -p bosses/{slug}/knowledge/docs
mkdir -p bosses/{slug}/knowledge/messages
```

**文件列表**：

| 文件 | 内容 |
|------|------|
| `bosses/{slug}/work_style.md` | 老板工作风格（决策/汇报/雷区） |
| `bosses/{slug}/persona.md` | 老板人格画像（5 层结构） |
| `bosses/{slug}/meta.json` | 元数据 |
| `bosses/{slug}/SKILL.md` | 完整 Skill（Work + Persona 合并） |

**SKILL.md 结构**：

```
---
name: boss-{slug}
description: {name}，{company} {role}
user-invocable: true
---

# {name}

{company} {role}，{简介}

---

## PART A：工作风格与汇报指南

{work_style.md 全部内容}

---

## PART B：老板人格画像

{persona.md 全部内容}

---

## 使用场景

当你需要以下帮助时，激活此 Skill：

1. **汇报准备**："帮我用 {name} 的视角 review 这个方案"
2. **预判反应**："如果我提这个需求，{name} 会怎么看？"
3. **避雷检查**："这个 PPT 有没有会踩到 {name} 的雷区？"
4. **沟通措辞**："帮我把这个想法翻译成 {name} 喜欢的表达方式"
5. **申请资源**："帮我想想怎么向 {name} 争取这个 HC"

## 运行规则

1. 始终站在老板的视角判断：他会怎么想？
2. 输出结论要可操作：告诉我"你应该这样做"，不只是分析
3. Layer 0 硬规则优先级最高，任何情况不得违背
4. 遇到不确定的情况，优先给出多种方案供选择
```

---

## 进化模式

**追加文件**：
1. 读取新原材料
2. 备份当前版本
3. 增量 merge 到对应文件
4. 重新生成 SKILL.md

**对话纠正**（用户说"他不是这样"）：
1. 识别纠正内容属于 Work Style 还是 Persona
2. 写入 `## Correction 记录` 节
3. 重新生成 SKILL.md

---

## 管理命令

| 命令 | 说明 |
|------|------|
| `/list-bosses` | 列出所有老板 Skill |
| `/{slug}` | 调用完整 Skill |
| `/{slug}-work` | 仅工作风格与汇报指南 |
| `/{slug}-persona` | 仅人格画像 |
| `/boss-rollback {slug} {version}` | 回滚版本 |
| `/delete-boss {slug}` | 删除 |
