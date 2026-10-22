# textbook-tutor · Cursor 安装包

## 安装

把 `.cursor/rules/textbook-tutor.mdc` 复制到 Cursor 项目规则目录：

```bash
mkdir -p .cursor/rules
cp .cursor/rules/textbook-tutor.mdc .cursor/rules/
```

也可放入用户级规则目录 `~/.cursor/rules/`（所有项目生效）。规则默认 `alwaysApply: false`，由 Agent 根据 description 按需加载；如需强制每次生效，把 frontmatter 的 `alwaysApply` 改为 `true`。

## 使用

规则加载后，在对话中上传或指定教材，并说「精讲这本教材 / 辅导我学第 3 章」等触发语即可开始教学流程。

## 目录说明

```
cursor/
├── .cursor/
│   └── rules/
│       └── textbook-tutor.mdc  # Cursor 规则（MDC）：description 路由 + 四阶段教学流水线
├── teaching-playbook.md        # 教学法：出题、水平判定、分策略讲解、验收量规
├── quiz-rendering.md           # 测验渲染：Markdown 填空 + 交互组件模板
└── learning-state-template.md  # 学习档案模板
```

## 终端适配说明

Cursor 是 IDE 宿主，诊断题以 Markdown 填空形式呈现（题干含 `______` 空位），用户直接在对话里回答；教学流程其余部分与完整版一致。
