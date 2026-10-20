# 填空诊断组件 Quiz Widget

诊断题的前端渲染规范。每次 Phase 3.1 出诊断题、或 Phase 3.3 发巩固小题时，按本文件渲染交互组件。用户答案通过 `sendPrompt()` 作为新消息回传给 agent——这就是插件的答案回传机制。

## 提交流程（答案回传协议）

```
agent 生成问题 → show_widget 渲染填空组件 → 用户填写并点「提交答案 ↗」
→ 组件调用 sendPrompt('【诊断题作答】知识点：X，我的答案：Y')
→ 该文本作为用户新消息回到 agent → agent 判定水平并继续教学
```

- 回传消息必须带协议头，agent 才能识别这是「对哪道诊断题/哪个知识点的作答」：
  - 正常作答：`【诊断题作答】知识点：勾股定理，我的答案：a²+b²=c²`
  - 明确不会：`【诊断题作答】知识点：勾股定理，我的答案：不会`
- 组件内答案判空：空值不收，聚焦输入框等用户填。
- sendPrompt 万一失败，降级为组件内提示用户把答案复制进聊天框。

## 填空组件模板（单空）

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

## 多空变体

题干含两个空时，加第二个输入框（`id="tt-ans2"`），提交时拼接：

```javascript
var v=document.getElementById('tt-ans').value.trim()+' / '+document.getElementById('tt-ans2').value.trim();
```

题干里两个空各用一个 `<span class="tt-blank">？</span>` 标出。

## 使用规则

- **一次只问一个知识点**，一个组件一道题。不要在组件里堆多题。
- 题干中用户要填的位置，用 `<span class="tt-blank">？</span>` 画出下划线空位，输入框统一放在题干下方，保持版面干净。
- 提示（HINT_TEXT）默认隐藏，用户点「要个提示」才展开——不提示也能做对，才能说明是真会。
- 标识徽标如实标注题型：诊断题 / 巩固题 / 挑战题。
- 遵守可视化组件设计规范：浅色主题、无渐变无阴影、正文字号 13–14px、无代码注释、按钮触发 sendPrompt 时带 ↗。
- 题干较长时用 `line-height:2.1` 保证空线和文字对齐呼吸感；空线宽度随答案预估长度调 `min-width`。
- 数学/化学符号让用户用纯文本写（如 `a^2`、`H2O`），agent 判读时自行换算，不要求用户记特殊格式。
