# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

教人如何**零成本**架設個人 AI 助手。三個核心元件（龍蝦比喻）：

| 比喻 | 元件 | 說明 |
|------|------|------|
| 龍蝦 | [OpenClaw](https://github.com/openclaw/openclaw) | 開源個人 AI 助手，支援 20+ 訊息頻道 |
| 池 | [Oracle Cloud Always Free](https://www.oracle.com/cloud/free/) | ARM VM (4 OCPU / 24 GB / 200 GB)，永久免費 |
| 飼料 | [Ollama Cloud](https://ollama.com) + [LiteLLM](https://github.com/BerriAI/litellm) | 免費模型 API + 統一 LLM gateway |

目標讀者：想免費架設個人 AI 助手的技術愛好者。語言：zh-TW。

## Architecture

```
使用者手機/電腦
    ↓ (LINE / Telegram / WhatsApp / ...)
Oracle Cloud ARM VM (Singapore)
    ├── OpenClaw Gateway (:18789)
    ├── LiteLLM Proxy (:4000) → Ollama Cloud / Gemini / OpenCode / ...
    └── Tailscale (VPN + Serve/Funnel)
```

## Docs Structure

| 文件 | 內容 | 狀態 |
|------|------|------|
| `docs/oracle-cloud-setup.md` | 建立免費的池子（Oracle Cloud ARM VM + Tailscale） | 完成 |
| `docs/openclaw-install.md` | 安裝龍蝦（OpenClaw） | 待撰寫 |
| `docs/litellm-setup.md` | 準備飼料（LLM 模型 + LiteLLM） | 完成 |

## Content Guidelines

- 避免洩漏真實 API key、IP、hostname，使用 placeholder（`<YOUR_VM_IP>`, `<YOUR_API_KEY>`）
- Oracle Cloud 步驟需標註已知陷阱
- 免費模型資訊變動快，寫作時確認最新狀態
