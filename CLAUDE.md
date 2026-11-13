# travel-plan

靜態 HTML 旅遊行程網站（GitHub Pages），每趟旅行一個資料夾，命名為 `YYYY-MM-DD-MM-DD-<地點>/`，主頁是該資料夾的 `index.html`。

## 行程摘要 `summary.md`

各行程的 HTML 很大且大多是 CSS/版面，閱讀行程內容時不要直接讀 HTML：

- 要了解某趟行程時，先讀該資料夾的 `summary.md`（純文字摘要：航班、住宿、每日行程、餐飲、待辦、HTML 區塊結構）。
- 只有在需要修改 HTML、或摘要沒有涵蓋的細節時，才去讀 `index.html`，並用 Grep／指定行數只讀相關段落。
- 修改 `index.html` 的行程內容時，同一次變更裡要同步更新 `summary.md`；兩者不一致時以 HTML 為準，並修正摘要。
- 某趟行程還沒有 `summary.md` 時，第一次需要讀它的內容就順便產生一份：用腳本去掉 `<style>`/`<script>`/標籤後抽出純文字再整理，不要把整份 HTML 讀進來。
