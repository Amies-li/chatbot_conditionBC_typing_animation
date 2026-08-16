# Chatbot Anthropomorphism Experiment — Stimulus Materials

HCI 眼動追蹤實驗刺激材料，研究聊天機器人擬人化設計對使用者感知的影響。

## 檔案說明

| 檔案 | 條件 | 說明 |
|------|------|------|
| `condition_A.html` | 條件 A | 無動畫對照組，Bot 回覆直接呈現 |
| `condition_BC.html` | 條件 B / C | 含切換器：B 為圓潤有機泡泡動畫，C 為尖銳爆炸星形動畫（SVG） |
| `condition_D.html` | 條件 D | 自我延伸元素組，含自我條件（照片上傳）與他人條件（SVG 陌生人頭像） |
| `design_guide.md` | — | 色彩系統、字型、AOI 規格、設計原則 |

## 使用方式

直接以瀏覽器開啟各 HTML 檔案即可，無需任何後端或安裝。

## 實驗設計

- **操弄因子**：動畫類型（無 / 圓潤 / 尖銳 / 自我延伸）
- **情境腳本**：客服等待超時（8 分鐘），觸發超時通知與三個快速回覆選項
- **測量方式**：眼動追蹤（AOI 分析）+ Godspeed 擬人化量表
- **理論框架**：CASA、自我延伸理論、副社會關係理論

## 技術規格

- 介面寬度：390 × 680–720 px（模擬行動裝置）
- 動畫時長統一：1.2s
- 字型：Noto Sans TC、DM Sans（B 組）；+ IBM Plex Mono（C 組 UI 層）
- 所有頭像以 Base64 內嵌，無需外部資源
