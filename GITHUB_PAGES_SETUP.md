# GitHub Pages 設定指南

本 repository 已設定好 GitHub Pages 的所有必要檔案。

## 已完成的設定

1. ✅ 建立 `/docs/index.html` - 主要登陸頁面
2. ✅ 建立 `/docs/.nojekyll` - 停用 Jekyll 處理
3. ✅ 建立 HTML 包裝頁面 - 用於顯示 markdown 文件

## 啟用 GitHub Pages

請依照以下步驟在 GitHub 上啟用 Pages：

1. 前往 repository 設定頁面：
   `https://github.com/yicianwang0629/openclaw-share/settings/pages`

2. 在 "Build and deployment" 區段：
   - **Source**: 選擇 "Deploy from a branch"
   - **Branch**: 選擇 `main` 分支（或你想部署的分支）
   - **Folder**: 選擇 `/docs`

3. 點擊 "Save"

4. 等待幾分鐘，GitHub 會自動部署網站

5. 完成後，網站將會發布在：
   `https://yicianwang0629.github.io/openclaw-share/`

## 檔案結構

```
docs/
├── .nojekyll              # 停用 Jekyll
├── index.html             # 主頁（美化的登陸頁面）
├── oracle-cloud-setup.html  # 教學 1 的 HTML 包裝
├── oracle-cloud-setup.md    # 教學 1 的原始 markdown
├── litellm-setup.html       # 教學 3 的 HTML 包裝
└── litellm-setup.md         # 教學 3 的原始 markdown
```

## 新增新的文件頁面

若要新增新的教學文件：

1. 在 `/docs` 建立 markdown 檔案（例如：`openclaw-install.md`）

2. 建立對應的 HTML 包裝檔案（例如：`openclaw-install.html`）：

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>你的文件標題</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/github-markdown-css@5/github-markdown.min.css">
    <style>
        /* 使用與其他頁面相同的樣式 */
    </style>
</head>
<body>
    <div class="container">
        <a href="index.html" class="back-link">← 返回首頁</a>
        <div id="content" class="markdown-body">Loading...</div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/marked@11/marked.min.js"></script>
    <script>
        fetch('你的檔案.md')
            .then(response => response.text())
            .then(text => {
                document.getElementById('content').innerHTML = marked.parse(text);
            });
    </script>
</body>
</html>
```

3. 在 `index.html` 更新文件列表，加入新的連結

## 注意事項

- `.nojekyll` 檔案很重要，它告訴 GitHub 不要用 Jekyll 處理檔案
- HTML 包裝檔案使用 `marked.js` CDN 來即時轉換 markdown
- 所有樣式使用 GitHub Markdown CSS 以保持一致性
