# textbook-tutor · 教材精讲私教

一个跨 harness 的 Agent 技能插件：把任意教材变成「先诊断、后讲解、讲完就练、学完验收」的个性化小课。

- 仓库根目录 = **WorkBuddy** 安装包（原生命令形态）
- `packs/` = 其他热门 harness 的安装包，**每个包自包含，单独拷贝即用**

## 它解决什么问题

- 拿到教材不知道从哪学起、自己学到什么程度
- 看教材时「一看就懂，一做就废」
- 学完一节说不清自己学了什么

插件让 agent 先摸清你对每个知识点的真实水平，再按水平定制讲解，并用一份持续更新的学习档案记住你的偏好、思维模式和常犯错误——学得越久，教得越准。

## 教学流水线

```
Phase 1  接入教材 → agent 快速识别学科与章节结构 → 询问学习起点（强制等待用户指定）
Phase 2  精读指定小节 → 输出完整知识点清单 → 建立学习档案
Phase 3  逐点循环：诊断填空题 → 判定 L0–L3 真实水平
         → 按水平定制讲解（生活化例子 + 幽默口吻）→ 讲完立即派巩固任务
Phase 4  合书自述总结 → 按量规评估 → 不合格针对性补习 → 合格后下发独立任务
         （生成小节过关卷，或推荐刷题资源）
```

每个环节都要求用户真实参与：起点由用户选、诊断由用户答、总结由用户写，agent 不代答、不跳过验收。

## 诊断题的跨宿主适配

| 宿主类型 | 渲染方式 | 答案回传 |
|---|---|---|
| 支持交互式 HTML 组件（WorkBuddy Visualizer） | 填空输入组件 | 按钮 `sendPrompt()` 回传，协议格式 `【诊断题作答】知识点：X，我的答案：Y` |
| 终端 / IDE 类（Claude Code、DSH、Codex、Gemini CLI、Cline、Cursor） | Markdown 填空题（题干含 `______` 空位） | 用户直接在对话里回答 |

## 安装矩阵

| Harness | 安装位置 | 操作 |
|---|---|---|
| **WorkBuddy** | `~/.workbuddy-ai/skills/textbook-tutor/` | 复制仓库根目录整个文件夹 |
| **Claude Code** | `~/.claude/skills/textbook-tutor/`（技能） | 复制 `packs/claude-code/skills/textbook-tutor/`；或按 `packs/claude-code/README.md` 以插件市场方式安装 |
| **DeepSeek Harness** | `~/.dsh/skills/textbook-tutor/`（或项目 `.dsh/skills/`） | 复制 `packs/deepseek-harness/textbook-tutor/`；skill 是 Markdown 指令集，`dsh plugin add` 装不了，详见包内 README |
| **OpenAI Codex CLI** | `~/.codex/prompts/` | 复制 `packs/codex-cli/prompts/textbook-tutor.md`；`/textbook-tutor` 调用，支持位置参数 |
| **Gemini CLI** | `~/.gemini/commands/`（或项目 `.gemini/commands/`） | 复制 `packs/gemini-cli/commands/textbook-tutor.toml`；`/textbook-tutor` 调用，支持 `{{args}}` |
| **Cline** | 项目 `.clinerules/`（或全局规则目录） | 复制 `packs/cline/.clinerules/textbook-tutor.md` |
| **Cursor** | 项目 `.cursor/rules/`（或 `~/.cursor/rules/`） | 复制 `packs/cursor/.cursor/rules/textbook-tutor.mdc` |
| **其他兼容 AGENTS.md 的 harness** | 项目 `AGENTS.md` | 追加 `packs/codex-cli/AGENTS.md-section.md` 的内容 |

安装后在对话中提及「教材 / 精讲 / 辅导我学 / 学习档案」等触发词即自动启用。

## 目录结构

```
textbook-tutor/
├── README.md                        # 本文件
├── SKILL.md                         # WorkBuddy 技能入口：四阶段教学流水线与红线
├── references/
│   ├── teaching-playbook.md         # 教学法：出题原则、水平判定量规、分策略讲解、验收量规
│   └── quiz-widget-html.md          # WorkBuddy 填空诊断组件模板与 sendPrompt 回传协议
├── assets/
│   └── learning-state-template.md   # 学习档案模板（状态表 + 用户画像 + 策略调整记录）
└── packs/                           # 其他 harness 安装包（各自自包含）
    ├── quiz-rendering.md            # 共享：测验渲染规范（Markdown 填空 + 交互组件两套方案）
    ├── claude-code/                 # .claude/skills + .claude-plugin 插件市场
    ├── deepseek-harness/            # .dsh/skills 指令集
    ├── codex-cli/                   # ~/.codex/prompts + AGENTS.md 条款
    ├── gemini-cli/                  # ~/.gemini/commands TOML
    ├── cline/                       # .clinerules
    └── cursor/                      # .cursor/rules MDC
```

## 使用方式

对 agent 说：「帮我学这本教材」，然后上传或指定教材文件（PDF / Word / 图片 / 文本均可）。agent 会先汇报学科定位，问你想从哪一节开始——指定后即进入完整的诊断—讲解—巩固—验收循环。

## 备注

- 学习档案保存在当前工作区 `学习档案/<教材名>-学习档案.md`，跨会话持续生效
- 本仓库仅包含技能定义文件，不含任何本地隐私信息
