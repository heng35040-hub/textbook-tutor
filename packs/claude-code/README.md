# textbook-tutor · Claude Code 安装包

两种安装方式任选。

## 方式一：作为技能安装（最简单）

把 `skills/textbook-tutor/` 整个目录复制到技能目录：

```bash
# 个人级（本机所有项目可用）
cp -r skills/textbook-tutor ~/.claude/skills/

# 项目级（仅当前仓库，提交后团队共享）
cp -r skills/textbook-tutor .claude/skills/
```

安装后在对话中提到「教材 / 精讲 / 辅导我学」即自动触发，也可用 `/textbook-tutor` 手动调用。

## 方式二：作为插件安装（可随市场分发）

本目录同时是一个 Claude Code 插件 + 本地市场：

```bash
# 在 Claude Code 中执行
/plugin marketplace add <本目录的绝对路径或 git 仓库地址>
/plugin install textbook-tutor@textbook-tutor-local
```

插件形态的技能命令为 `/textbook-tutor:textbook-tutor`。

## 目录说明

```
claude-code/
├── .claude-plugin/
│   ├── plugin.json        # 插件清单（方式二用）
│   └── marketplace.json   # 本地市场索引（方式二用）
└── skills/
    └── textbook-tutor/
        ├── SKILL.md                  # 技能入口：四阶段教学流水线
        ├── teaching-playbook.md      # 教学法：出题、水平判定、分策略讲解、验收量规
        ├── quiz-rendering.md         # 测验渲染：Markdown 填空 + 交互组件模板
        └── learning-state-template.md # 学习档案模板
```

## 终端适配说明

Claude Code 是终端宿主，诊断题以 Markdown 填空形式呈现（题干含 `______` 空位），用户直接在对话里回答；教学流程的其余部分（诊断判定、分水平讲解、巩固任务、合书验收、学习档案）与完整版一致。
