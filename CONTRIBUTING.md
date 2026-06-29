# 貢獻指南

感謝你對這個專案有興趣！這是一個單一 `index.html` 檔的 PWA，沒有打包流程，貢獻門檻很低。

## 回報問題 / 提出功能建議

請開一個 [Issue](https://github.com/yangchanlin-svg/ycla-site-photo-mapper/issues)，盡量包含：

- 使用的裝置與瀏覽器（例如：iPhone Safari / Android Chrome PWA / 桌面 Chrome）
- 重現步驟
- 預期行為與實際行為的差異
- 如果跟雲端同步有關，請附上畫面右上角雲朵狀態徽章的顏色/文字截圖

## 送 Pull Request

1. Fork 本專案，建立分支
2. 直接用瀏覽器打開 `index.html` 測試（見 [README - 本機開發](README.md#本機開發)）
3. 修改後，請同步更新：
   - `index.html` 裡的 `#app-version-display` 版本號
   - `sw.js` 裡的 `CACHE_NAME`（兩者需一致，否則 Service Worker 會繼續提供舊版快取）
   - `CHANGELOG.md` 補上這次變更的一行說明
4. 提交 PR，描述清楚改了什麼、為什麼改

## 規範

- 沿用既有風格：Tailwind CSS（CDN）+ Lucide Icons，沒有額外的 CSS/JS 框架
- 不要引入打包工具（webpack/vite 等）——這個專案刻意保持「丟到任何靜態主機就能跑」
- 涉及 Firebase/Google Drive 的改動，請在 PR 說明中標註會不會影響既有使用者的資料結構（IndexedDB schema、Firestore 文件結構）
