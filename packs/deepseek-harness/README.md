# textbook-tutor · DeepSeek Harness 安装包

## 重要概念：skill 不是 plugin

DeepSeek Harness（DSH）里 **skill 是给模型的可加载 Markdown 指令集**，靠本地提供方扫描目录发现；**`dsh plugin add` 装的是插件（代码扩展包），装不了 skill**。所以本包的正确安装方式是「把目录放进 skill 根目录」，不是走插件安装命令。

## 安装：复制到 skill 根目录

DSH 的本地 skill 提供方按 rank 扫描六个根目录，rank 越小越优先：

| rank | 位置 | 适用场景 |
|---|---|---|
| 100 | `<项目根>/.dsh/skills` | 只给当前项目用 |
| 200 | `<项目根>/.agents/skills` | 项目内多个 agent 工具共享 |
| 300 | `Config.customSkillDirs` | 自定义目录 |
| 400 | `~/.dsh/skills`（`$DSH_HOME`） | 全局给自己用 |
| 500 | `~/.agents/skills`（`$DSH_AGENTS_HOME`） | 全局给自己用 |
| 600 | 随包目录 | 需配置 `Config.bundledSkillDir` |

**全局安装（任何项目可用）：**

```bash
mkdir -p ~/.dsh/skills
cp -r textbook-tutor ~/.dsh/skills/
```

**项目级安装（仅当前项目）：**

```bash
mkdir -p .dsh/skills
cp -r textbook-tutor .dsh/skills/
```

注意：skill 必须落在根目录的**直属位置**，不支持 `**/SKILL.md` 嵌套递归；项目根的判定是「包含 `.git` 的最近祖先目录」。

## 使用

安装后无需重启，注册表不缓存完整定义。两种触发方式：

- **模型自动加载**：在对话里提到「教材 / 精讲 / 辅导我学」等触发词，模型读到目录中的 `description` 后自动加载本 skill；
- **手动调用**：直接输入 `/textbook-tutor`。

## 目录说明

```
deepseek-harness/
└── textbook-tutor/           # 整个目录拷到 skill 根目录下
    ├── SKILL.md              # 技能入口：name + description 路由 + 四阶段教学流水线
    ├── teaching-playbook.md  # 教学法：出题、水平判定、分策略讲解、验收量规
    ├── quiz-rendering.md     # 测验渲染：Markdown 填空 + 交互组件模板
    └── learning-state-template.md # 学习档案模板
```

## 终端适配说明

DSH 终端/桌面会话中，诊断题以 Markdown 填空形式呈现（题干含 `______` 空位），用户直接在对话里回答；教学流程其余部分（诊断判定、分水平讲解、巩固任务、合书验收、学习档案）与完整版一致。DSH Web 端若安装了 genui / visualize 类回复渲染插件，可改用 `quiz-rendering.md` 中的交互填空组件方案。

## 想做成插件分发？

本包是 skill 形态；如需走 `dsh plugin add` 的插件链路（npm 包 + `apply(ctx)` + `dsh-plugin` topic），可在此基础上另行封装 Cordis 插件，把 SKILL.md 内容作为指令资源注册。skill 形态已覆盖全部教学功能，插件封装只是分发方式的差异。
