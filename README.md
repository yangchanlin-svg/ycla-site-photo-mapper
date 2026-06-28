# YCLA-工地相片定位標籤系統

一個給建築師 / 工地監造人員用的拍照定位工具：在建築平面圖上點一個位置、設定拍照方向、拍一張現場照片，系統會自動產生帶編號的標記點，最後可以匯出成一份附平面索引圖 + 編號照片明細表的 PDF 檢查報告書。

線上版本：https://yangchanlin-svg.github.io/ycla-site-photo-mapper/

開發紀錄：見 [CHANGELOG.md](CHANGELOG.md)

> **目前仍在內部測試階段**，功能與資料結構都可能還會調整，暫不開放外部 Issue / PR，敬請期待正式開源的時間點。

## 特色

- **免登入也能用**：本機上傳平面圖、拍照、編號、輸出報告書，完全不需要帳號，單機可用。
- **登入後跨裝置同步**：用 Google 帳號登入後，專案、平面圖、標記點位置會自動透過 Firebase 同步；現場照片則自動上傳到使用者自己的 Google Drive（`drive.file` 範圍，只能存取這個 App 自己建立的檔案），其他裝置登入同一帳號就能自動補抓回來。
- **多圖頁管理**：一個專案可以有多張平面圖（例如每個樓層一張），各自獨立編號。
- **自動編碼**：可設定字首與位數（例如 `3F-001`），新標記點自動編號。
- **方向指示**：每個標記點可設定拍照方向（指南針 UI），事後仍可調整。
- **PWA**：可加到手機主畫面，離線可用（Service Worker 快取）。
- **報告書匯出**：A4 橫式／直式可切換，平面索引圖與照片明細表自動分頁，可直接列印或另存 PDF。

## 技術架構

整個 App 是**一個檔案**：`index.html`（含內嵌的 `<style>` 與 `<script>`），沒有打包流程、沒有 npm install，直接打開瀏覽器或丟到任何靜態主機就能跑。

- **本機儲存**：IndexedDB（`projects` / `floorplans` / `markers` 三個 store），免登入時的唯一資料來源。
- **跨裝置同步（結構性資料）**：[Firebase](https://firebase.google.com/)（Auth + Firestore）。只同步專案、圖頁、標記點的「位置/方向/編號」等結構性資料，**不存照片本體**，避免需要升級到付費的 Blaze 方案。
- **跨裝置同步（照片本體）**：[Google Drive API v3](https://developers.google.com/drive/api/v3/about-sdk)，照片上傳到使用者自己的 Drive（固定使用 `01_plan`／`02_Current photos` 兩個子資料夾）。
- **Google 授權**：身份登入用 Firebase Auth（`GoogleAuthProvider`）；Drive 存取權杖改用 [Google Identity Services](https://developers.google.com/identity/gsi/web) 的 `initTokenClient` 直接取得 —— 這是因為 Firebase 的 `signInWithRedirect` 會經過 `firebaseapp.com` 中介網域，在手機上已安裝的 PWA 環境下常會遺失跨網域暫存狀態，GIS 直接從網站自己的網域發起請求，比較穩定。
- **PDF 平面圖**：[PDF.js](https://mozilla.github.io/pdf.js/) 在瀏覽器端直接把上傳的 PDF 渲染成圖片。
- **UI**：[Tailwind CSS](https://tailwindcss.com/)（CDN 版）+ [Lucide Icons](https://lucide.dev/)。

## 本機開發

不需要安裝任何東西，直接用瀏覽器打開 `index.html` 即可（部分功能如 Service Worker 在 `file://` 協定下無法註冊，屬正常現象，可用任意靜態伺服器跑，例如 `npx serve .`）。

雲端同步相關功能（登入、跨裝置同步、Drive）需要自己的 Firebase 專案：

1. 在 [Firebase Console](https://console.firebase.google.com/) 建立專案，啟用 Authentication（Google 提供者）與 Firestore。
2. 在 Google Cloud Console 的 OAuth 用戶端設定中，把你的網站網域加入「已授權的 JavaScript 來源」。
3. 把 `index.html` 開頭 `<script type="module">` 裡的 `firebaseConfig` 換成你自己專案的設定值，以及 `GOOGLE_OAUTH_CLIENT_ID` 換成你的 Web 用戶端 ID。

（Firebase 的 `apiKey` 是公開的用戶端識別碼，不是密鑰，安全性由 Firestore Security Rules 把關，不需要特別隱藏。）

## 部署

目前部署在 GitHub Pages，`main` 分支 push 之後會自動重新建置。`index.html` 與 `sw.js` 裡各有一個版本號（`#app-version-display` 與 `CACHE_NAME`），兩者必須同步更新，否則 Service Worker 會繼續提供舊版快取，使用者重新整理也看不到變化。

## 參與貢獻

目前專案仍在內部測試階段，暫不開放外部 Issue / Pull Request。等測試穩定、正式對外開源後，這裡會補上完整的貢獻指南。

## 授權

[GPL-3.0](LICENSE)
