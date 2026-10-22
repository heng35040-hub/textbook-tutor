# textbook-tutor · OpenAI Codex CLI 安装包

## 安装

把 `prompts/textbook-tutor.md` 复制到 Codex 自定义提示词目录：

```bash
mkdir -p ~/.codex/prompts
cp prompts/textbook-tutor.md ~/.codex/prompts/
```

## 使用

- 在 Codex CLI 中输入 `/textbook-tutor` 手动调用；
- 支持位置参数：`/textbook-tutor <教材路径> [起始位置]`，例如 `/textbook-tutor ./physics.pdf 第3章`；
- 不带参数调用时，按流程先询问教材位置和学习起点。

另可把 `AGENTS.md-section.md` 的内容追加到项目的 `AGENTS.md`（或全局 `~/.codex/AGENTS.md`），让 Codex 在相关对话中自动遵循触发规则，无需手动调用命令。

## 目录说明

```
codex-cli/
├── prompts/
│   └── textbook-tutor.md      # 自定义提示词：YAML frontmatter + 四阶段教学流水线
├── AGENTS.md-section.md       # 可追加到 AGENTS.md 的触发条款
├── teaching-playbook.md       # 教学法：出题、水平判定、分策略讲解、验收量规
├── quiz-rendering.md          # 测验渲染：Markdown 填空 + 交互组件模板
└── learning-state-template.md # 学习档案模板
```

## 终端适配说明

Codex CLI 是终端宿主，诊断题以 Markdown 填空形式呈现（题干含 `______` 空位），用户直接在对话里回答；教学流程其余部分与完整版一致。
