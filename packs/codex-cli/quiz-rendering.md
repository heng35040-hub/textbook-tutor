# 测验渲染规范 Quiz Rendering

诊断题/巩固题的前端渲染规范。**终端类宿主**（Claude Code、Codex CLI、Gemini CLI、DeepSeek Harness、Cline、Cursor）用方案 A；**支持交互式 HTML 组件的宿主**（如 WorkBuddy Visualizer）用方案 B。

---

## 方案 A：Markdown 填空（终端类宿主默认）

终端里没有输入组件，用 Markdown 画出填空，然后**停下等用户在对话里回答**。

渲染格式：

```markdown
**诊断题 · 知识点：勾股定理**

直角三角形两条直角边长为 3 和 4，则斜边长 = ______ 。

（凭直觉答，答错不扣分；不会就回「不会」。）
```

规则：

- 题干里要填的位置用连续下划线 `______` 标出，输入提示放在题干下方括号内。
- 一次只问一个知识点，一道题。
- **出题后必须停止输出，等待用户下一轮回答**。严禁自问自答、严禁在题目后面附带讲解或答案。
- 用户回复的任何内容都是有效信号，包括「不会」「不知道」。
- 用户答完，按 `teaching-playbook.md` 的 L0–L3 量规判定水平，再进入讲解。

回传协议（终端里用户天然就是用聊天回复，无需 sendPrompt）：

- 正常作答：用户直接打出答案文本。
- 明确不会：用户回复「不会」。
- agent 侧只需在收到回复后确认「这是对哪道诊断题/哪个知识点的作答」——上下文里紧跟其前的题目即为该题，无需协议头。

---

## 方案 B：交互填空组件（宿主支持 HTML 组件时用）

当宿主支持把交互式 HTML 组件渲染进对话（如 WorkBuddy 的 show_widget / Visualizer）时，用填空输入组件：用户填答案、点提交，答案经 `sendPrompt()` 作为新消息回传给 agent。

回传消息必须带协议头，agent 才能识别这是「对哪道诊断题/哪个知识点的作答」：

- 正常作答：`【诊断题作答】知识点：勾股定理，我的答案：a²+b²=c²`
- 明确不会：`【诊断题作答】知识点：勾股定理，我的答案：不会`

### 填空组件模板（单空）

占位符替换规则：`KP_NAME`=知识点名，`QUESTION_TEXT`=含空线的题干，`HINT_TEXT`=提示语。

```html
<h2 class="sr-only">知识点前置诊断填空题，填写答案提交给学习教练</h2>
<style>
.tt-q{font-size:14px;line-height:2.1;color:var(--color-text-primary);margin:0 0 4px}
.tt-blank{display:inline-block;min-width:96px;border-bottom:1.5px solid var(--color-border-secondary);text-align:center;color:var(--color-text-tertiary);font-size:13px}
.tt-row{display:flex;gap:10px;align-items:center;margin-top:14px}
.tt-tip{font-size:12px;color:var(--color-text-secondary);margin:10px 0 0;display:none}
</style>
<div style="max-width:640px">
  <div style="display:flex;align-items:center;gap:8px;margin-bottom:10px">
    <span style="font-size:13px;color:var(--color-text-secondary)">诊断题</span>
    <span style="font-size:12px;padding:2px 10px;border-radius:999px;background:var(--color-background-info);color:var(--color-text-info)">KP_NAME</span>
  </div>
  <p class="tt-q">QUESTION_TEXT <span class="tt-blank">？</span></p>
  <input id="tt-ans" type="text" placeholder="凭直觉填，答错不扣分" style="width:100%;margin-top:12px" onkeydown="if(event.key==='Enter')ttSubmit()">
  <div class="tt-row">
    <button onclick="ttSubmit()">提交答案 ↗</button>
    <button onclick="document.getElementById('tt-tip').style.display='block'">要个提示</button>
    <button onclick="ttGiveUp()">这题我不会 ↗</button>
  </div>
  <p class="tt-tip" id="tt-tip">HINT_TEXT</p>
</div>
<script>
function ttSubmit(){
  var v=document.getElementById('tt-ans').value.trim();
  if(!v){document.getElementById('tt-ans').focus();return;}
  var msg='【诊断题作答】知识点：KP_NAME，我的答案：'+v;
  try{sendPrompt(msg)}catch(e){document.getElementById('tt-tip').style.display='block';document.getElementById('tt-tip').textContent='提交失败，请把以下内容复制到聊天框发送：'+msg;}
}
function ttGiveUp(){
  var msg='【诊断题作答】知识点：KP_NAME，我的答案：不会';
  try{sendPrompt(msg)}catch(e){alert(msg)}
}
</script>
```

### 多空变体

题干含两个空时，加第二个输入框（`id="tt-ans2"`），提交时拼接：

```javascript
var v=document.getElementById('tt-ans').value.trim()+' / '+document.getElementById('tt-ans2').value.trim();
```

题干里两个空各用一个 `<span class="tt-blank">？</span>` 标出。

---

## 通用使用规则（两种方案都适用）

- **一次只问一个知识点**，一个组件/一道题。不要堆多题。
- 提示默认隐藏，用户点「要个提示」才展开——不提示也能做对，才能说明是真会。
- 标识徽标如实标注题型：诊断题 / 巩固题 / 挑战题。
- 数学/化学符号让用户用纯文本写（如 `a^2`、`H2O`），agent 判读时自行换算，不要求用户记特殊格式。
- 终端方案下，题干中的下划线空位长度随答案预估调整；行文保持短段落。
