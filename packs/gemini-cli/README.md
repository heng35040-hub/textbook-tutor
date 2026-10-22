# textbook-tutor · Gemini CLI 安装包

## 安装

把 `commands/textbook-tutor.toml` 复制到 Gemini CLI 自定义命令目录：

```bash
# 用户级（所有项目可用）
mkdir -p ~/.gemini/commands
cp commands/textbook-tutor.toml ~/.gemini/commands/

# 项目级（仅当前项目）
mkdir -p .gemini/commands
cp commands/textbook-tutor.toml .gemini/commands/
```

## 使用

- 在 Gemini CLI 中输入 `/textbook-tutor` 手动调用；
- 支持参数注入：`/textbook-tutor <教材路径> [起始位置]`，例如 `/textbook-tutor ./physics.pdf 第3章`；
- 不带参数调用时，按流程先询问教材位置和学习起点。

## 目录说明

```
gemini-cli/
├── commands/
│   └── textbook-tutor.toml   # 自定义命令：description + 四阶段教学流水线
├── teaching-playbook.md      # 教学法：出题、水平判定、分策略讲解、验收量规
├── quiz-rendering.md         # 测验渲染：Markdown 填空 + 交互组件模板
└── learning-state-template.md # 学习档案模板
```

## 终端适配说明

Gemini CLI 是终端宿主，诊断题以 Markdown 填空形式呈现（题干含 `______` 空位），用户直接在对话里回答；教学流程其余部分与完整版一致。
