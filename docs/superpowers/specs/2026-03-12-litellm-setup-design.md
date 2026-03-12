# Design: 準備飼料 — LLM 模型 + LiteLLM

> **Target file**: `docs/litellm-setup.md`
> **Date**: 2026-03-12
> **Status**: Approved

## Purpose

教讀者如何取得免費（及低成本）的 LLM 模型 API，並透過 LiteLLM Proxy 統一管理、load balance、自動 fallback。延續龍蝦比喻：飼料 = LLM 模型。

## Target Audience

已完成 `docs/oracle-cloud-setup.md`（有 Oracle ARM VM + Tailscale SSH）的讀者。

## Key Decisions

| 決策 | 結論 |
|------|------|
| 文件範圍 | 免費方案為主，進階（小錢）為補充 |
| Google AI Pro $10 GCP credits | 已驗證每位家庭成員各自獲得 $10 |
| LiteLLM 深度 | 完整安裝 + 設定教學（在 Oracle VM 上） |
| 多帳號策略 | 直接教，附 ToS 注意提醒 |
| 文件結構 | 方案 A：飼料分層（why → 免費 → 小錢 → 安裝 → 接上 OpenClaw） |
| PostgreSQL | 不裝（簡化版，省去 spend logging） |
| Service manager | systemd（Oracle VM = Ubuntu） |

## Document Structure

```
# 準備飼料 — LLM 模型 + LiteLLM

## 飼料策略總覽
  - 開場：龍蝦比喻延續
  - 架構圖（Mermaid）：OpenClaw → LiteLLM → 多 Provider
  - 為什麼需要 LiteLLM（統一格式、多 key LB、fallback）
  - 成本總覽表（純免費 / +Go / +Google AI Pro 家庭）
  - 前置需求

## Phase 1：免費飼料（$0）

### Ollama Cloud（主角）
  - 介紹：Ollama Cloud 是什麼
  - 可用模型表（GLM-5, Kimi K2.5, MiniMax M2.5, Qwen 3.5, Gemini 3 Flash）
  - 免費額度限制（light usage, hourly + weekly）
  - 多帳號策略：
    - 註冊多個帳號（A, B, C, D）
    - 各自取得 API key
    - 後面 LiteLLM 會教怎麼配置
  - 附註：ToS 注意

### Gemini API Free Tier
  - 到 aistudio.google.com 申請 API key
  - 免費模型：Gemini 3.1 Flash Lite
  - 限制：RPM/RPD 較嚴（2025-12 後被砍 50-80%）
  - 同樣可多帳號各申請 key
  - 注意：Gemini 3 Flash / 3.1 Pro 僅限付費

## Phase 2：進階飼料（花一點小錢大幅提升）

### OpenCode Go（$10/月）
  - 介紹：OpenCode 的付費 model 訂閱
  - 模型：GLM-5, Kimi K2.5, MiniMax M2.5
  - 200 requests / 5hr window
  - 與 Ollama 同模型但走付費通道，更穩定
  - LiteLLM 中免費/付費用不同 pool name 隔離
  - 簡提 OpenCode 本身也是好用的 coding agent

### Google AI Pro 家庭方案策略（亮點）
  - 核心概念圖（tree/mermaid）
  - $19.99/月 ÷ 6 人 ≈ $3.33/人
  - 每位成員各自獲得：
    - 獨立 Gemini API key
    - $10/月 GCP credits（已驗證）
    - 6 人 × $10 = $60/月
  - 步驟：
    1. 購買 Google AI Pro
    2. 啟用家庭共享
    3. 邀請 5 位家人
    4. 各自申請 Gemini API key
    5. 各自領取 Google Developer Program (GDP) Premium → $10 GCP credits
    6. 收集 6 把 key → LiteLLM
  - 成本效益分析表

### 成本彙總表
  | 組合 | 月費 | 你得到什麼 |
  |------|------|-----------|
  | 純免費 | $0 | Ollama 多帳號 + Gemini Free key |
  | + OpenCode Go | $10 | + 穩定通道 |
  | + Google AI Pro 家庭 | ~$13.33 | + 6 把 paid key + $60 GCP |
  | 全部 | ~$23.33 | 10+ 模型、多重 fallback |

## Phase 3：安裝 LiteLLM（在 Oracle VM 上）

> 以下步驟皆在 Oracle VM 上執行（透過 Tailscale SSH 連入）。

### Step 1：安裝
  - 安裝 uv
  - `uv tool install 'litellm[proxy]'`（不裝 PostgreSQL）

### Step 2：建立 .env
  - 放所有 API key
  - `chmod 600`

### Step 3：寫 config.yaml
  - 範例 config.yaml，涵蓋：
    - 1 個 Ollama Cloud pool（4 key × 3 model）
    - 1 個 Gemini Free pool（3 key × Flash Lite）
    - 1 個 Gemini Paid pool（6 key × Flash + Pro，進階章節）
    - fallbacks 設定
  - 重點解說：
    - 同 model_name 多 deployment = 自動 LB
    - simple-shuffle = 隨機分配
    - fallbacks 寫法
    - 免費/付費用不同 model_name 隔離
  - ⚠️ 高曝光提醒（config 範例中用 comment 強調）：
    Ollama Cloud 必須用 `ollama_chat/*` 格式（不是 `openai/*`），
    否則 stream 時 reasoning_content 不輸出

### Step 4：啟動 + 測試
  - 前景啟動測試
  - curl health + chat completions

### Step 5：設為 systemd 服務
  - systemd unit file 範例
  - enable + start

### 驗證清單
  - [ ] `curl http://localhost:4000/health/readiness` 回 200
  - [ ] `curl http://localhost:4000/models` 列出所有 pool
  - [ ] 對每個 pool 發一則測試訊息，確認回覆正常
  - [ ] 故意停用一把 key，確認 fallback 生效

## Phase 4：接上 OpenClaw

> 本段假設你已完成 openclaw-install.md 的安裝。如果尚未安裝 OpenClaw，請先完成該教學再回來。

### 設定 OpenClaw 指向 LiteLLM
  - 使用 `openclaw models auth` 新增 LiteLLM 作為 provider
  - 或編輯 `~/.openclaw/openclaw.json` 設定 model provider endpoint 為 `http://localhost:4000`
  - 使用 `openclaw models set litellm/<model-name>` 切換模型
  - （完整 OpenClaw 安裝見 openclaw-install.md）

### 端到端驗證
  - 手機發訊息 → OpenClaw → LiteLLM → 模型回覆
  - 確認 fallback 正常

### 驗證清單
  - [ ] 從手機（LINE/Telegram）發訊息，收到 AI 回覆
  - [ ] `openclaw gateway usage-cost --days 1` 可看到 token 消耗

## 已知陷阱一覽

| # | 陷阱 | 後果 | 避免方式 |
|---|------|------|---------|
| 1 | Ollama Cloud 用 openai/* 格式 | stream 時 reasoning 不輸出 | 用 ollama_chat/* |
| 2 | 免費/付費 key 混在同一 pool | 免費限制拖慢付費 | 不同 model_name |
| 3 | LiteLLM background health check | 燒光 Ollama 週配額 | config.yaml 設 `background_health_checks: false` |
| 4 | Gemini 免費層 RPD 限制 | 一天用完就沒了 | 多 key + fallback |
| 5 | Google AI Pro credits 需主動領取 | 不領就浪費 | 每人到 developers.google.com 領取 GDP Premium |

## 下一步
  - → openclaw-install.md（安裝龍蝦）

## 參考資料
  - Ollama Cloud: https://ollama.com/cloud
  - Gemini API: https://ai.google.dev/gemini-api/docs
  - OpenCode Go: https://opencode.ai/go
  - Google AI Pro: https://one.google.com/about/google-ai-plans/
  - LiteLLM: https://docs.litellm.ai/
```
