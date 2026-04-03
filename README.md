# 老板.skill

> 把你的老板蒸馏成 AI Skill，读懂他的决策逻辑，每次汇报都稳了。

## 和同事 Skill 的区别

| | 同事.skill | 老板.skill |
|--|-----------|-----------|
| **核心目标** | 让 AI 替他工作 | 让 AI 帮你理解他、预判他 |
| **主要场景** | Code Review、技术方案、日常协作 | 汇报准备、申请资源、向上管理 |
| **Persona 重点** | 他的技术规范和工作习惯 | 他的决策逻辑和雷区 |
| **使用方式** | "用 XX 的方式帮我做 Y" | "如果我提 X，他会怎么反应？" |

## 安装

```bash
# 安装到当前项目
mkdir -p .claude/skills
git clone https://github.com/colinwps/boss-skill .claude/skills/create-boss

# 或安装到全局
git clone https://github.com/colinwps/boss-skill ~/.claude/skills/create-boss
```

## 使用

```
/create-boss
```

按提示输入老板花名、基本信息、你们的汇报关系、你对他的印象，然后选择原材料来源。

完成后用 `/{slug}` 调用该老板 Skill。

## 目录结构

```
boss-skill/
├── SKILL.md                          # Skill 入口
├── prompts/
│   ├── boss_work_analyzer.md         # 工作风格分析器
│   └── boss_persona_builder.md       # 人格画像生成器（5 层结构）
├── bosses/
│   └── example_boss/                 # 示例：L总
│       └── SKILL.md                  # 完整示例 Skill
└── README.md
```

## 支持的标签

**管理风格**：PPT 控 · 数据驱动 · 微操 · 放权型 · 结果导向 · 过程导向

**企业文化**：字节范 · 阿里味 · 腾讯味 · 华为味

**性格**：话少但有话必中 · 会议前必须对齐 · 最怕没有结论 · 特别在意竞品 · 情绪化

## 使用场景示例

```
用户      ❯ 我今天要向 L总 申请再招 2 个前端，帮我准备一下

老板.skill ❯ 根据 L总 的风格，他的第一反应一定是"现有的人为什么不够"。
             你需要提前准备：
             1. 当前团队的时间分配表（数据说话）
             2. 这 2 个 HC 能带来的具体 ROI（DAU 预期提升多少）
             3. Plan B：如果他不批，你打算怎么取舍现有工作

             开场白建议：
             "L总，我想和您汇报一下前端资源现状，我有一个具体的申请，
             数据在这里……"

             不要说："我感觉人手不够，想再招 2 个人。"
```

## License

MIT © 2026
