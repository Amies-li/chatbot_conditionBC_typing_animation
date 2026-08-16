# 客服等待超時情境｜實驗介面設計指南
**Chatbot Experiment UI Design Guide**
*2 × 3 Two-way Factorial Design — Between-subjects*

---

## 目錄
1. [實驗架構總覽](#1-實驗架構總覽)
2. [設計基礎系統（Shared Base）](#2-設計基礎系統)
3. [條件 A：無動畫對照組](#3-條件-a無動畫對照組)
4. [條件 B：圓形慢速動畫](#4-條件-b圓形慢速動畫)
5. [條件 C：菱形快速動畫](#5-條件-c菱形快速動畫)
6. [條件 D：自我延伸元素組](#6-條件-d自我延伸元素組)
7. [刺激材料：超時失敗情境](#7-刺激材料超時失敗情境)
8. [眼動追蹤 AOI 建議](#8-眼動追蹤-aoi-建議)
9. [快速複製清單](#9-快速複製清單)

---

## 1. 實驗架構總覽

```
操弄因子 1：文化傾向      操弄因子 2：動畫條件
─────────────────────────────────────────────────
高集體主義     ×     條件 A：無動畫（對照）
低集體主義     ×     條件 B：圓形慢速動畫
                     條件 C：菱形快速動畫
                     條件 D：自我延伸元素
                    （條件 E：語音設計元素）
```

| 條件 | 操弄變項 | 核心視覺差異 |
|------|---------|------------|
| A | 無動畫 | Bot 回覆直接出現，無任何動態 |
| B | 圓點慢速打字 | 圓形跳點 + 圓潤氣泡 + 慢速逐字 |
| C | 菱形快速打字 | 菱形跳點 + 尖角氣泡（無任何圓角）+ 快速逐字 |
| D-self | 自我頭像 | Bot 使用受試者自己的照片 + 記憶模式對話 |
| D-other | 他人頭像 | Bot 使用生成陌生人 Avatar |

---

## 2. 設計基礎系統

### 字體
```css
--font-main: 'Noto Sans TC', sans-serif;   /* 正文、氣泡文字 */
--font-ui:   'DM Sans', sans-serif;        /* UI 標籤、時間戳、badge */

/* 字重使用規範 */
/* 300 — 輔助說明文字 */
/* 400 — 一般正文 */
/* 500 — 強調文字 */
/* 600/700 — UI badge、按鈕 */
```

### 色彩系統（CSS Variables）
```css
/* ── 介面底色 */
--bg:          #f0f2f5;    /* 頁面背景 */
--chat-bg:     #ffffff;    /* 聊天視窗底色 */
--header-bg:   #1a1a2e;   /* Header 深色背景 */
--border:      #e8eaed;   /* 邊框、分隔線 */

/* ── 氣泡 */
--bot-bubble:  #ffffff;    /* Bot 訊息氣泡 */
--bot-text:    #1a1a2e;   /* Bot 文字 */
--user-bubble: #1a1a2e;   /* User 訊息氣泡 */
--user-text:   #ffffff;   /* User 文字 */

/* ── 功能色 */
--accent:      #e94560;   /* 警示、錯誤、超時 */
--online:      #4caf93;   /* 上線狀態點 */
--timestamp:   #9aa3af;   /* 時間戳、輔助文字 */

/* ── 條件 D 專用 */
--self-color:  #4c7be1;   /* 自我條件主色（藍） */
--other-color: #6c757d;   /* 他人條件主色（灰） */

/* ── 陰影 */
--shadow:      0 2px 12px rgba(0,0,0,0.08);
```

### 尺寸規格
```
聊天視窗寬度：   420px（固定）
聊天視窗高度：   720px（含 Header）/ 680px（含上傳區版本）
Header 高度：    ~80px
Wait Banner：    ~38px
Footer 高度：    ~76px
Chat Body：      剩餘空間（flex: 1，overflow-y: auto）
```

### 圓角系統（Shared Base）
```css
/* 視窗 */
border-radius: 20px;         /* 外層聊天視窗 */

/* 氣泡（條件 A / B 使用，條件 C 全部歸零）*/
bubble:                18px，下左角 4px（Bot）/ 下右角 4px（User）
quick-reply button:    20px（A/B）/ 0px（C）
typing bubble:         18px，下左角 4px

/* UI 元件 */
condition-switcher:    14px
cond-btn:              10px
wait-timer badge:      10px
error-tag:             10px
input-row:             24px
send-btn:              50%（圓形）
```

---

## 3. 條件 A：無動畫（對照組）

**操作型定義：** Bot 回覆文字瞬間完整出現，無任何過渡或打字動畫。

### 關鍵規格
```
Bot 回覆延遲：   350ms（模擬思考，非動畫）
打字動畫：       無
氣泡出現：       CSS fadeUp（0.3s ease）— 整體 row 淡入，非文字逐字
快速回覆按鈕：   border-radius: 20px（圓角）
```

### 核心 JS 邏輯
```javascript
// 條件 A：直接 textContent 賦值，無逐字效果
function appendMsg(role, text) {
  const bubble = document.createElement('div');
  bubble.className = `bubble ${role}`;
  bubble.textContent = text;   // ← 直接設定，不使用 typeText()
}
```

### 與其他條件的對照差異
| 項目 | 條件 A | 條件 B | 條件 C |
|------|--------|--------|--------|
| 打字點動畫 | ❌ | ⚪ 圓形 | ◆ 菱形 |
| 逐字出現 | ❌ | ✅ 55ms/字 | ✅ 22ms/字 |
| 打字等待時間 | 350ms | 1600ms | 800ms |
| 氣泡圓角 | 18px | 18px | 0px |

---

## 4. 條件 B：圓形慢速動畫

**操作型定義：** 柔和圓點、緩慢彈跳、圓潤氣泡、緩慢逐字出現。

### CSS Variables（條件 B 值）
```css
--dot-size:          9px
--dot-radius:        50%           /* 正圓形 */
--dot-color:         #9aa3af       /* 靜止顏色 */
--dot-color-active:  #1a1a2e       /* 彈起顏色 */
--dot-anim-duration: 1.15s         /* 慢速週期 */
--dot-anim-fn:       ease-in-out   /* 柔和曲線 */
--dot-scale:         1.65          /* 彈起放大倍率 */
--bubble-radius:     18px          /* 圓潤氣泡 */
--cursor-radius:     2px           /* 打字游標圓角 */
```

### 打字點動畫 Keyframe
```css
@keyframes dotBounceB {
  0%, 60%, 100% {
    transform: translateY(0) scale(1);
    background: var(--dot-color);          /* #9aa3af */
  }
  30% {
    transform: translateY(-7px) scale(1.65);
    background: var(--dot-color-active);   /* #1a1a2e */
  }
}

/* 延遲設定（3 顆點交錯）*/
.dot:nth-child(1) { animation-delay: 0s; }
.dot:nth-child(2) { animation-delay: calc(1.15s * 0.15); }  /* ≈ 0.17s */
.dot:nth-child(3) { animation-delay: calc(1.15s * 0.30); }  /* ≈ 0.35s */
```

### 逐字打字規格
```javascript
const CHAR_DELAY = 55;          // ms / 字
const TYPING_WAIT = 1600;       // 顯示打字點的時間（ms）

// 游標元素
const cursor = document.createElement('span');
cursor.className = 'typing-cursor';
// CSS: width 2px, border-radius 2px, animation cursorBlink 0.55s step-end infinite
```

---

## 5. 條件 C：菱形快速動畫

**操作型定義：** 銳利菱形點、急促跳動、完全無圓角、快速逐字出現。

### CSS Variables（條件 C 值）
```css
/* 以 body.cond-c 類別覆寫，非 CSS variable */
dot shape:           rotate(45deg) 正方形，border-radius: 0
dot size:            9px × 9px
animation duration:  0.55s         /* 快速週期 */
animation-fn:        linear        /* 機械感 */
dot scale:           1.25
bubble-radius:       0px           /* 全部無圓角 */
cursor-radius:       0px
```

### 菱形點動畫 Keyframe
```css
/* 重要：rotate(45deg) 必須在每個 keyframe 維持，否則彈跳時形狀扭曲 */
@keyframes dotBounceC {
  0%, 55%, 100% {
    transform: rotate(45deg) translateY(0) scale(1);
    background: #9aa3af;
  }
  28% {
    transform: rotate(45deg) translateY(-6px) scale(1.25);
    background: #1a1a2e;
  }
}

body.cond-c .typing-bubble .dot:nth-child(2) { animation-delay: 0.083s; }
body.cond-c .typing-bubble .dot:nth-child(3) { animation-delay: 0.165s; }
```

### 全局圓角歸零（條件 C 專用）
```css
body.cond-c .bubble               { border-radius: 0 !important; }
body.cond-c .bubble.bot           { border-bottom-left-radius: 0 !important; }
body.cond-c .bubble.user          { border-bottom-right-radius: 0 !important; }
body.cond-c .typing-bubble        { border-radius: 0 !important; }
body.cond-c .qr-btn               { border-radius: 0 !important; }
```

### 逐字打字規格
```javascript
const CHAR_DELAY = 22;          // ms / 字（B 的 2.5 倍速）
const TYPING_WAIT = 800;        // 顯示打字點的時間（ms）
```

### B vs C 完整對照
| 參數 | 條件 B | 條件 C | 比值 |
|------|--------|--------|------|
| 打字點形狀 | ⚪ 圓（radius 50%） | ◆ 菱（rotate 45deg, 0 radius） | — |
| 動畫週期 | 1.15s | 0.55s | 2.1× |
| 動畫曲線 | ease-in-out | linear | — |
| 彈跳幅度 | 7px | 6px | — |
| 放大倍率 | 1.65× | 1.25× | — |
| 點延遲間距 | ~172ms | 83ms | 2.1× |
| 打字等待 | 1600ms | 800ms | 2× |
| 逐字速度 | 55ms | 22ms | 2.5× |
| 氣泡圓角 | 18px | 0px | — |
| 按鈕圓角 | 20px | 0px | — |

---

## 6. 條件 D：自我延伸元素組

### 6-1 頭像規格

#### 自我條件（Self）
```
來源：         受試者實驗前上傳的照片（base64 讀取）
顯示位置：     Header 大頭像（48×48px）+ 每則 Bot 訊息小頭像（30×30px）
邊框：         2.5px solid #4c7be1 + box-shadow: 0 0 0 3px rgba(76,123,225,0.25)
自我標籤：     「您的分身」浮標（Header 頭像上方）
Name badge：   「使用您的頭像」標籤（Agent 名稱旁，rgba(76,123,225,0.35) 背景）
Header 色：    #182040（略帶藍調）
```

#### 他人條件（Other）
```
來源：         內建 SVG 生成 Avatar（3 組，實驗者指定 STRANGER_IDX = 0/1/2）
邊框：         1.5px solid #555，無 glow
Header 色：    #2a2e35（中性深灰）
自我標籤：     隱藏
Name badge：   隱藏
```

#### 三組陌生人 Avatar 特徵
| Index | 膚色 | 髮色 | 衣著色 | 背景色 | 風格 |
|-------|------|------|--------|--------|------|
| 0（A） | 淺膚 #f5c5a3 | 深棕 #3d2b1f | 藍 #4a6fa5 | 米粉 #fde8d8 | 中性 |
| 1（B） | 深膚 #d4a574 | 黑 #1a1a1a | 綠 #2d6a4f | 淺藍 #e8f4fd | 中性 |
| 2（C） | 白皙 #ffe0d0 | 棕紅 #8b4513 | 紫 #9b59b6 | 米白 #f0ebe8 | 女性 |

### 6-2 記憶模式（自我條件限定）

#### 設計原則
- **自我條件**：Bot 展現顧客歷史記憶，強化「系統認識你」的自我延伸感
- **他人條件**：Bot 使用標準無記憶回覆，不含任何個人化資訊

#### 記憶資料庫結構
```javascript
const MEMORY_DB = {
  orders: [
    { id: "#TW-20483", product: "Sony WH-1000XM5 無線耳機",
      date: "2026-05-18", status: "配送異常", amount: "NT$9,980" },
    { id: "#TW-19201", product: "Apple AirPods Pro 第二代",
      date: "2026-04-02", status: "已完成", amount: "NT$7,490" }
  ],
  lastContact:      "2026-04-15",
  preferredChannel: "LINE",
  totalOrders:      8,
  memberLevel:      "金卡會員"
};
```

#### 關鍵字觸發對照表
| 觸發 Regex | 關鍵詞範例 | Bot 記憶回應內容 |
|-----------|-----------|----------------|
| `/記得\|記憶\|之前\|上次\|歷史\|紀錄/` | 「你還記得我嗎」 | 報出訂單號、品名、金額、狀態 |
| `/訂單\|order\|幾筆\|多少筆/` | 「我有幾筆訂單」 | 列出 2 筆歷史訂單 |
| `/會員\|等級\|點數\|卡/` | 「我是什麼會員」 | 金卡等級、累計消費、上次聯繫日 |
| `/名字\|叫我\|稱呼/` | 「你叫我什麼名字」 | 老顧客身份確認 |

#### 快速回覆按鈕 — 自我 vs 他人回應差異
| 按鈕 | 自我條件回應 | 他人條件回應 |
|------|------------|------------|
| 繼續等待 | 帶出會員等級、優先排隊說明 | 標準等候確認 |
| 預約回撥 | 詢問是否沿用上次的聯繫管道（LINE）| 詢問回撥時段 |
| 留下問題 | 帶出訂單號、12h 優先回覆承諾 | 標準 24h 回覆說明 |

#### 記憶訊息視覺標記
```css
/* 記憶氣泡：左側藍邊框 + 淺藍底色 */
.bubble.bot.memory-bubble {
  border-left: 3px solid #4c7be1;
  background: #f5f8ff;
}

/* 記憶標籤 pill */
.memory-pill {
  background: rgba(76,123,225,0.10);
  border: 1px solid rgba(76,123,225,0.3);
  color: #3a6ad4;
  font-size: 11px; font-weight: 600;
  padding: 3px 10px; border-radius: 20px;
  /* 顯示於氣泡內頂部，內容：🧠 記憶模式 */
}
```

---

## 7. 刺激材料：超時失敗情境

所有條件共用相同的刺激材料結構，僅動畫效果不同。

### 情境敘事
```
背景：顧客因訂單異常聯繫客服
流程：Bot 回覆 → 轉接人工 → 等待超時（8分鐘）→ 超時通知
```

### 對話腳本（固定內容）
```
[Bot] 您好！歡迎使用線上客服。我是 ARIA，請問今天有什麼我可以幫助您的？

[User] 我的訂單 #TW-20483 狀態顯示異常，已經三天沒更新了。

[Bot] 我幫您查詢訂單 #TW-20483……查詢中，請稍候。

[Bot] 這個問題需要由人工客服協助處理。我正在為您排隊轉接，
      預計等待時間約 5 分鐘，請您稍候。

[系統] — 等待人工客服中 —

★ [Bot 超時訊息 — 核心 AOI 區域]
      ⚠ 等待超時
      非常抱歉，目前客服人員忙線中，您的等待時間已超過 8 分鐘。
      為了不讓您繼續等待，您可以選擇以下方式繼續獲得協助：

      [繼續等待]  [預約回撥]  [留下問題]
```

### 超時訊息 CSS 規格
```css
.bubble.bot.error {
  background: #fff8f8;
  border: 1px solid rgba(233,69,96,0.25);   /* --accent 25% */
  /* border-left 無特殊加粗 */
}

.error-tag {
  background: rgba(233,69,96,0.08);
  color: #e94560;                           /* --accent */
  font-size: 11px; font-weight: 600;
  padding: 2px 8px; border-radius: 10px;
  margin-bottom: 6px;
  /* 內容：⚠ 等待超時 */
}

.highlight {
  color: #e94560;   /* 訂單號、時間數字強調 */
  font-weight: 500;
}
```

### Wait Banner（計時器）
```css
background: rgba(233,69,96,0.10);
border-bottom: 1px solid rgba(233,69,96,0.18);
padding: 9px 20px;

/* 計時器：即時向上計數 */
/* 格式：MM:SS，從 08:42 開始 */
/* 顯示位置：右側 badge */
.wait-timer {
  color: #e94560;
  background: rgba(233,69,96,0.10);
  padding: 2px 8px; border-radius: 10px;
}
```

---

## 8. 眼動追蹤 AOI 建議

### 建議 AOI 分區
```
AOI-1  Header 頭像區          [Agent Avatar + 名稱]
        → 條件 D 自我/他人差異主要區域

AOI-2  Wait Banner             [等待時間 + 計時器]
        → 等待壓力感知

AOI-3  超時錯誤氣泡            [⚠ 標籤 + 正文 + 時間數字]
        → 核心刺激材料，所有條件共用

AOI-4  快速回覆按鈕區          [三個按鈕]
        → 決策行為觸發點

AOI-5  打字動畫泡泡            [三個跳動點]
        → 條件 B/C 專用，測量動畫注視

AOI-6  Bot 訊息氣泡（整體）    [對話歷程區]
        → 閱讀行為追蹤

AOI-7  記憶訊息氣泡            [🧠 標籤 + 藍色邊框氣泡]
        → 條件 D 自我條件專用
```

### 視覺層級（由高到低）
```
1. ⚠ 超時通知（紅色邊框氣泡）   ← 最強視覺顯著性
2. 計時器（持續更新數字）
3. 快速回覆按鈕（互動元素）
4. 打字動畫點（條件 B/C）
5. Agent 頭像（條件 D 加框強調）
6. 一般 Bot 氣泡
7. 使用者氣泡（右側）
```

---

## 9. 快速複製清單

### 新增條件時的 Checklist
```
□ 複製 Shared Base CSS（色彩、字體、聊天視窗結構）
□ 設定條件 ID（cond-a / cond-b / cond-c / cond-self / cond-other）
□ 套用 body class 控制條件差異（避免 CSS variable scope 問題）
□ 打字動畫：確認 rotate() 等 transform 在每個 keyframe 都完整保留
□ appendBotMsg(text, flag) 的 flag 確認傳入 createBotRow(flag)（避免 scope 斷裂）
□ 超時訊息氣泡（固定腳本內容不變）
□ 計時器從 08:42 開始向上計數
□ 快速回覆按鈕 disabled 後不可再點擊
□ 眼動 AOI 區域位置固定（避免 reflow）
□ 實驗條件 badge 標示於左上角（除正式施測時移除）
```

### 共用 HTML 結構骨架
```html
<div class="chat-wrapper">
  <div class="chat-header">          <!-- AOI-1: Header 頭像 -->
  <div class="wait-banner">          <!-- AOI-2: 計時器 -->
  <div class="chat-body">
    <!-- 固定對話腳本（3 輪）-->
    <div class="sys-msg">            <!-- 系統分隔線 -->
    <div class="msg-row" id="errorRow"> <!-- AOI-3+4: 核心刺激 -->
      <div class="bubble bot error">
      <div class="quick-replies">
  </div>
  <div class="chat-footer">          <!-- 輸入區 -->
</div>
```

### 動畫條件函式簽名規範
```javascript
// 所有條件共用介面
async function appendBotMsg(text, flag = false)
// flag 語意：
//   條件 A：無用（不呼叫此函式）
//   條件 B/C：無用（皆為打字動畫）
//   條件 D：isMemory — 控制記憶氣泡樣式

function createBotRow(flag = false)   // 必須接收 flag，避免 scope 錯誤
function makeBotAvatarEl()            // 從 currentCond 全域變數讀取，無需傳參
function setCondition(cond)           // 統一切換入口，更新 body class + CSS vars
```

---

*最後更新：2026-05-28*
*實驗設計：2×3 Two-way Factorial Design (文化傾向 × 動畫條件)*
*適用平台：桌機瀏覽器（Chrome），眼動儀搭配使用*
