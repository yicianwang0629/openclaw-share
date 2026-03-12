# GitHub Pages 設定指南

此 repository 已經準備好 GitHub Pages 的內容。請依照以下步驟啟用：

## 啟用 GitHub Pages

1. 前往 repository 的 Settings 頁面
   - URL: `https://github.com/yicianwang0629/openclaw-share/settings/pages`

2. 在 "Build and deployment" 區塊中：
   - **Source**: 選擇 "Deploy from a branch"
   - **Branch**: 選擇主分支（通常是 `main` 或 `master`）和 `/docs` 資料夾
   - 點擊 "Save"

3. 等待幾分鐘後，網站將會部署到：
   - **URL**: `https://yicianwang0629.github.io/openclaw-share/`

## 檔案說明

- `docs/index.html` - 教學網站的主頁面
- `docs/.nojekyll` - 告訴 GitHub Pages 不要使用 Jekyll 處理檔案
- `docs/oracle-cloud-setup.md` - Oracle Cloud 設定教學
- `docs/litellm-setup.md` - LiteLLM 設定教學

## 測試

啟用後，可以透過以下方式測試：

1. 訪問 `https://yicianwang0629.github.io/openclaw-share/`
2. 檢查所有連結是否正常運作
3. 確認教學文件可以正確顯示

## 更新網站

每次 push 到主分支的 `docs/` 資料夾時，GitHub Pages 會自動重新部署網站。
