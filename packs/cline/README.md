# textbook-tutor · Cline 安装包

## 安装

把 `.clinerules/textbook-tutor.md` 复制到 Cline 规则目录：

```bash
# 项目级（VSCode 工作区根目录）
mkdir -p .clinerules
cp .clinerules/textbook-tutor.md .clinerules/

# 全局规则目录（Cline 设置中配置的 Global Rules 目录）
cp .clinerules/textbook-tutor.md <你的全局规则目录>/
```

## 使用

规则文件加载后全程生效。在对话中上传或指定教材，并说「精讲这本教材 / 辅导我学第 3 章」等触发语即可开始教学流程。

## 目录说明

```
cline/
├── .clinerules/
│   └── textbook-tutor.md     # Cline 规则：四阶段教学流水线
├── teaching-playbook.md      # 教学法：出题、水平判定、分策略讲解、验收量规
├── quiz-rendering.md         # 测验渲染：Markdown 填空 + 交互组件模板
└── learning-state-template.md # 学习档案模板
```

## 终端适配说明

Cline 是 IDE 内宿主，诊断题以 Markdown 填空形式呈现（题干含 `______` 空位），用户直接在对话里回答；教学流程其余部分与完整版一致。
