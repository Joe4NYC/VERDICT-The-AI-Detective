# 🤖 AI 製作 VERDICT 遊戲 — Prompt 指南 & 製作流程

---

## 📋 使用哪個 AI 工具？

| 工具 | 用途 | 建議 |
|---|---|---|
| **Claude (claude.ai)** | 生成完整 HTML/CSS/JS 代碼 | ✅ 首選，最擅長複雜 web app |
| **Cursor** | 在 IDE 裡 AI 輔助 debug/修改 | ✅ 配合 Claude 使用 |
| **ChatGPT GPT-4o** | Claude 出錯時的備用 | 備用 |

**最快流程**：把下方 Master Prompt 貼入 **Claude**，一次生成整個遊戲 HTML 檔案。

---

## 🚀 PHASE 1 — Master Prompt（貼入 Claude 直接生成完整遊戲）

---複製以下全部內容，貼入 Claude 對話框---

```
Build me a complete, single-file browser game called "VERDICT: The AI Detective".
Output ONE complete HTML file with all CSS and JavaScript embedded inline.
The game MUST be fully functional without any backend — just open the HTML file in a browser.

═══════════════════════════════════════
GAME CONCEPT
═══════════════════════════════════════
A murder mystery detective game where the player interrogates 4 AI-powered suspects
using Google Gemini API. Each suspect has unique personality and hidden secrets.
The player collects clues, cross-examines suspects, and issues a final VERDICT.

═══════════════════════════════════════
THE CASE STORY
═══════════════════════════════════════
VICTIM: Victor Ashford, 62, wealthy art collector
SCENE: His private mansion study, found dead at 10:15 PM
CAUSE: Poisoned wine glass found on desk
TIME OF DEATH: Between 9:30 PM and 10:15 PM

THE 4 SUSPECTS:

1. THOMAS CHEN — Personal Butler (15 years service)
   Personality: Overly formal, nervous, over-explains minor details
   Secret: He was outside the mansion between 9:45-10:10 PM meeting someone
   Lie: He will deny any conflict with Victor over unpaid wages
   Knows: Lady Ashford has been secretly selling Victor's artworks

2. LADY ELEANOR ASHFORD — Victor's wife (married 8 years)  
   Personality: Cold, aristocratic, deflects with counter-questions
   Secret: She is having an affair and was in the garden at 9:40 PM
   Lie: She will deny knowing about the updated will that cuts her inheritance by 50%
   Knows: Dr. Mercer visited Victor privately two days ago about debts

3. DR. RAYMOND MERCER — Victor's business partner (20 years)
   Personality: Charming, overly helpful, changes subject when cornered
   Secret: Victor was about to expose him for embezzling £200,000 from their firm
   Lie: He will deny being in the mansion after 8:00 PM (he was there at 9:50 PM)
   Knows: Thomas Chen has a key to the private wine cellar

4. SOPHIE HARTLEY — Victor's niece, 28, art student
   Personality: Casual, uses humour to deflect, gets defensive when pressed
   Secret: She was the last person to see Victor alive at 9:55 PM, they argued
   Lie: She will deny knowing the contents of Victor's new will (she inherits everything)
   Knows: Lady Ashford had access to exotic plants from the greenhouse

ACTUAL MURDERER: DR. RAYMOND MERCER
(He poisoned Victor's wine glass during his secret 9:50 PM visit to prevent exposure)

═══════════════════════════════════════
5 GAME SCREENS (implement as JS view switching, no page reload)
═══════════════════════════════════════

SCREEN 1 — CASE BRIEFING
- Full-screen dark noir opening
- Show case title, victim photo placeholder (CSS-generated silhouette), 
  case details card (victim, time, cause, location)
- Bottom: "BEGIN INVESTIGATION" primary button
- Ambient style: candlelight warmth, aged paper texture via CSS

SCREEN 2 — SUSPECT BOARD  
- Header: "SUSPECTS" + evidence count badge + "EVIDENCE BOARD" tab button
- 4 suspect cards in a 2×2 grid
- Each card: suspect portrait (CSS avatar initials), name, title, status badge
- Status badges: NOT INTERVIEWED (grey) / INTERVIEWED (amber) / SUSPICIOUS (red)
- Click card → go to SCREEN 3 (Interrogation Room) for that suspect
- Add "ISSUE VERDICT" button (disabled until at least 3 suspects interviewed)

SCREEN 3 — INTERROGATION ROOM
- Split layout: left 35% suspect info panel | right 65% chat area
- Suspect info panel: large avatar, name, title, relationship to victim, 
  current status badge, "BACK TO SUSPECTS" button
- Chat area: scrollable message history, typing indicator (animated dots)
- Bottom input: text field + "ASK" button + 3-minute countdown timer
- Each AI response has "📌 ADD TO EVIDENCE" button
- When timer hits 0: auto-dismiss with "Session ended" message
- Timer resets if player leaves and returns

SCREEN 4 — EVIDENCE BOARD
- Pin-board aesthetic: dark cork background
- Grid of evidence cards, each showing: suspect name tag, the quote text, timestamp
- Empty state: "No evidence collected yet. Start interrogating suspects."
- "ANALYZE CONTRADICTIONS" button → sends all evidence to Gemini and streams 
  an AI analysis of what contradictions were found
- Back button to Suspect Board

SCREEN 5 — VERDICT ROOM
- Appears when player clicks "ISSUE VERDICT" on Suspect Board
- Show all 4 suspects with radio buttons to select one
- Text area: "State your case" — player writes their reasoning (optional)
- "SUBMIT VERDICT" button
- After submit: Gemini (as Judge character) streams a dramatic verdict response
  - If correct (Dr. Mercer): congratulate, reveal the full true story
  - If wrong: "Insufficient evidence" — explain what was missed, encourage retry
- Option to play again

═══════════════════════════════════════
GEMINI API INTEGRATION
═══════════════════════════════════════

API Setup:
- Use Google Generative AI JavaScript SDK via ESM CDN:
  import { GoogleGenerativeAI } from "https://esm.run/@google/generative-ai";
- On game start: show a modal asking for Gemini API Key input
  (store in a JS variable, never in localStorage)
- Model: gemini-2.0-flash (fast and free tier friendly)

Suspect System Prompts:
- Each suspect has a dedicated systemInstruction matching the character profiles above
- Maintain separate conversation history arrays per suspect (persists during session)
- Send full conversation history with each API call
- Wrap in try/catch: on error show "The suspect refuses to answer. Try again."

Streaming Output:
- Use generateContentStream() for ALL Gemini calls
- Render each chunk with a typewriter append function
- Show animated "..." typing indicator before stream starts
- Disable input during streaming, re-enable when stream completes

Judge System Prompt (for VERDICT and ANALYZE CONTRADICTIONS):
"You are Judge Harrison, a stern British magistrate overseeing this murder trial.
For verdicts: speak dramatically, reference specific evidence mentioned, reveal 
the truth after the verdict is delivered. Keep under 200 words.
For contradiction analysis: list specific contradictions found between suspect 
statements as numbered points. Be analytical and precise. Under 150 words."

═══════════════════════════════════════
VISUAL DESIGN
═══════════════════════════════════════

Color Palette (dark noir murder mystery):
- Background: #0d0b08 (near black with warm tint)
- Surface: #161310
- Surface elevated: #1f1b16
- Surface card: #252018  
- Accent gold: #c9973a (amber/gold for key elements)
- Accent red: #8b2a2a (for SUSPICIOUS badge, danger states)
- Text primary: #e8e0d0
- Text muted: #8a8070
- Text faint: #4a4438
- Divider: #2a2520
- Success green: #2d5a3d

Typography:
- Display/Headings: 'Playfair Display' from Google Fonts (serif, classic detective)
- Body/UI: 'Inter' from Google Fonts (clean, readable)
- Monospace quotes: use font-style: italic on Playfair Display

Atmosphere:
- Subtle grain overlay on all surfaces (SVG feTurbulence filter at 3% opacity)
- Gold accent lines for section separators (1px, 30% opacity)
- Cards have box-shadow: 0 4px 20px rgba(0,0,0,0.5)
- Smooth transitions between all screens (opacity + translateY, 300ms ease)
- Hover states on suspect cards: slight gold border glow

═══════════════════════════════════════
TECHNICAL REQUIREMENTS
═══════════════════════════════════════
- Single HTML file, all CSS in <style>, all JS in <script type="module">
- Import Gemini SDK via: https://esm.run/@google/generative-ai
- Import Lucide icons via: https://unpkg.com/lucide@latest
  Initialize with: lucide.createIcons() after DOM updates
- Google Fonts: Playfair Display + Inter (preconnect + stylesheet link)
- NO localStorage or sessionStorage (use JS variables only)
- Responsive: works on 1024px+ desktop and 768px tablet
- All screen transitions use CSS classes + JS toggle (no page reload)
- Add a subtle animated particle/dust effect in the background using Canvas
  (very slow-moving golden dust motes, max 30 particles, low opacity)

═══════════════════════════════════════
EXTRA POLISH (implement these for full marks)
═══════════════════════════════════════
1. When a suspect is marked SUSPICIOUS (player right-clicks their card or 
   long-presses), add a red fingerprint stamp CSS effect
2. When evidence is added, animate the card flying to the evidence board 
   with a CSS keyframe animation
3. The 3-minute interrogation timer turns amber at 1 minute, red at 30 seconds
4. Add a subtle heartbeat sound effect when verdict is submitted
   (use Web Audio API to generate a simple low thump, no external audio files)
5. Case file number and timestamp in top-right corner of all screens

Deliver the complete, working HTML file. Include a comment block at the top
with setup instructions (how to get a Gemini API key).
```

---複製到此為止---

---

## 🔧 PHASE 2 — 修補 Prompt（遇到問題時使用）

### 如果 AI 回應不夠有個性：
```
The suspect responses feel too generic. Please rewrite the systemInstruction 
for each suspect to be MORE distinctive:
- Thomas: should stammer slightly, use overly formal language like "I must insist", 
  say "If I may be so bold" when deflecting
- Eleanor: should be icily polite, use cutting remarks, reference social class
- Dr. Mercer: should be TOO friendly, compliment the detective, use lawyer-like 
  qualifications like "to the best of my recollection"
- Sophie: should use modern slang, be sarcastic, laugh things off with "Ha! Seriously?"
Update all 4 system prompts and regenerate the suspects.js section.
```

### 如果 Streaming 不流暢：
```
The typewriter streaming effect is choppy. Please fix the renderChunk() function:
1. Append each chunk character by character with 15ms delay between characters
2. Auto-scroll the chat container to bottom after each append
3. Show a blinking cursor (CSS animation) at the end of the streaming text
4. Only remove cursor when stream is fully complete
```

### 如果畫面太暗看不清楚：
```
The text contrast is too low on dark backgrounds. Please:
1. Increase all --color-text values to be brighter (minimum #d0c8b8)
2. Make card backgrounds slightly lighter (#2a2418 minimum)
3. Ensure all interactive button text has sufficient contrast
4. Add a subtle inner glow to input fields on focus: box-shadow: 0 0 0 2px #c9973a40
```

### 如果要加入更多動畫：
```
Add these micro-animations to enhance the game feel:
1. Suspect cards: on hover, the avatar slightly scales up (1.05x) with a gold ring
2. Evidence board: new cards animate in with a "drop from top + bounce" keyframe
3. Timer: at 30 seconds remaining, the timer div pulses red with a CSS animation
4. Verdict screen: the suspect photos shake slightly when not selected, stop when selected
5. All button clicks: brief scale down (0.95x) on :active
```

---

## 🎨 PHASE 3 — 視覺升級 Prompt

```
The game looks functional but needs visual polish. Please enhance:

1. HERO SCREEN: Add an animated SVG magnifying glass logo that slowly rotates.
   Below it: "VERDICT" in large Playfair Display, with a subtitle 
   "Can you unmask the killer?" in smaller italic text.
   Add a slow animated gradient background using @property CSS custom property.

2. SUSPECT CARDS: Replace initials avatars with CSS-art portraits.
   Each suspect gets a unique geometric CSS illustration:
   - Thomas: butler silhouette with white collar details
   - Eleanor: elegant profile with pearl necklace dots  
   - Dr. Mercer: glasses + tie made from CSS shapes
   - Sophie: messy bun + paint smear detail

3. EVIDENCE BOARD: Style it like a real detective board with:
   - Aged paper texture on evidence cards (CSS noise grain filter)
   - Red string between contradicting pieces (CSS line using absolute positioning)
   - Push-pin dots at top of each card
   - Slight rotation variation on cards (random ±3deg per card)

4. VERDICT SCREEN: When the correct verdict is delivered, trigger:
   - A confetti animation (pure CSS keyframes, no library)
   - The correct suspect card glows and enlarges
   - "CASE CLOSED" stamp appears with a rubber-stamp animation
```

---

## 🚀 PHASE 4 — 最終提交準備 Prompt

```
Before I submit this game, please do a final quality pass:

1. Add a README comment block at the very top of the HTML file with:
   - Game title and one-line description
   - How to get a free Gemini API key (aistudio.google.com)
   - How to run (just open in browser)
   - Team/author placeholder

2. Add a small "i" info button in the top-right that shows a modal with:
   - Game instructions (how to interrogate, collect evidence, issue verdict)
   - Keyboard shortcut: Enter to submit question, Escape to go back

3. Make sure the game works on mobile (768px):
   - Suspect cards become single column
   - Interrogation room stacks vertically (suspect info on top, chat below)
   - Input stays fixed at bottom of screen
   - Evidence board cards become full-width

4. Add a "HOW TO PLAY" overlay on first launch 
   (only shown once per session, dismissed by clicking anywhere)

5. Final check: ensure all Gemini API calls have proper error handling with 
   user-friendly error messages (not raw error objects)
```

---

## ⏱️ 8 小時 AI 製作時間分配

| 時段 | 動作 | 用哪個 Prompt |
|---|---|---|
| **0:00–0:05** | 去 aistudio.google.com 取得免費 Gemini API Key | — |
| **0:05–0:30** | 貼入 Master Prompt 到 Claude，等待生成 | Phase 1 |
| **0:30–1:00** | 在瀏覽器測試生成的遊戲，記錄問題 | — |
| **1:00–2:00** | 用修補 Prompt 修正 bug 和 AI 個性 | Phase 2 |
| **2:00–3:30** | 用視覺升級 Prompt 改善美觀 | Phase 3 |
| **3:30–4:30** | 在 Cursor 手動微調細節（顏色、間距） | Cursor |
| **4:30–5:30** | 繼續玩測試遊戲，確保所有路徑都能走通 | — |
| **5:30–7:00** | 自由加功能或繼續 polish | 自由發揮 |
| **7:00–7:30** | 最終提交準備 Prompt | Phase 4 |
| **7:30–8:00** | 錄 demo 影片（備用）、寫 README、提交 | — |

---

## 💡 使用 Cursor 的技巧

如果你用 **Cursor IDE** 開啟 HTML 檔案，可以用以下內建 prompt 模式：

### Inline Edit（選取代碼後按 Cmd+K）:
```
Make the suspect card hover effect more dramatic — add a golden shimmer 
animation that sweeps across the card on hover
```

### Chat（Cmd+L）:
```
The Gemini API streaming is not working. The error in console is: [貼上錯誤]
Please fix the api.js section of my code.
```

### Composer（Cmd+Shift+I）— 大幅修改時用:
```
I want to add a new feature: a "NOTEBOOK" screen where the player can 
write their own notes during the investigation. Add it as a 4th tab 
in the navigation alongside "SUSPECTS" and "EVIDENCE BOARD".
```

---

## ⚠️ 常見 AI 生成問題 & 快速修法

| 問題 | 原因 | 解決方法 |
|---|---|---|
| API key modal 不顯示 | JS module scope 問題 | 告訴 Claude: "The API key modal doesn't appear on load. Fix the initialization order." |
| 嫌疑人回應是英文 | 沒指定語言 | 在每個 systemInstruction 加: "Always respond in English with a formal British accent." |
| Streaming 顯示 [object Object] | chunk 解析錯誤 | "Fix renderChunk() — it shows [object Object] instead of text. chunk.text() should be called correctly." |
| 畫面切換沒有動畫 | CSS transition class 問題 | "The screen transitions are instant, no animation. Fix the showScreen() function to use opacity transition." |
| 計時器不會重置 | clearInterval 漏掉 | "The interrogation timer doesn't reset when switching suspects. Fix the timer logic in showScreen()." |

---

## 🎯 Demo 當天指引

進入 Demo 前確認：
- [ ] Gemini API Key 已輸入，API 能正常回應
- [ ] 所有 4 位嫌疑人都能對話
- [ ] 「加入證物」功能正常
- [ ] 「發表裁決」→ AI 法官能回應
- [ ] 背景粒子效果在運行
- [ ] 備用：截圖 + 錄影備份（以防 API 在 demo 時掛掉）

Demo 說話重點：
"Traditional mystery games use scripted dialogue trees. 
VERDICT uses live Gemini API calls — every suspect is a real AI with 
memory, personality, and secrets. The same case plays differently every time."
