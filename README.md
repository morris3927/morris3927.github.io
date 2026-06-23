# Morris Blog

> 一個重新回頭念書的工程師,記錄讀過的論文筆記、歷年網頁作品與一些想法。

🔗 線上網址:<https://morris3927.github.io/>

以 [Hugo](https://gohugo.io/) + [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 打造的靜態部落格,並套用一套自訂的 editorial 風格(全站走 PaperMod CSS 變數,支援淺/深色)。

---

## 技術 & 部署

- **產生器**:Hugo(extended)
- **主題**:PaperMod(以 git submodule 掛在 `themes/PaperMod`)
- **部署**:push 到 `main` → GitHub Actions(`.github/workflows/gh-pages.yml`)自動建置並發佈到 `gh-pages`

> ⚠️ 主題是 submodule,**請勿直接修改 `themes/PaperMod/` 內的檔案**——那些改動不會進版控、部署時會被官方版覆蓋。所有客製都放在專案根目錄的 `layouts/` 與 `assets/`(Hugo 會自動覆蓋主題)。

## 本地預覽

```bash
# 第一次 clone 要連同 submodule(主題)一起拉
git clone --recurse-submodules https://github.com/morris3927/morris3927.github.io.git
# 或已 clone:git submodule update --init --recursive

# 啟動本地伺服器(-D 顯示草稿;baseURL 指定 localhost 避免連結指向正式站)
hugo server -D --baseURL http://localhost:1313/
```

開 <http://localhost:1313/> 預覽。

## 目錄結構

```
content/
├─ posts/papers/     論文筆記(主要寫作區)
├─ posts/            一般文章(歸在分類「文章」)
├─ projects/         作品集(資料驅動,見 data/projects.yaml)
├─ about.md          關於我
├─ archives/         所有文章(年表)
└─ tags/             標籤
data/projects.yaml   作品集資料來源
static/images/       圖片(含 projects/<slug>/ 作品截圖)
archetypes/papers.md 論文筆記範本
layouts/             自訂版型(覆蓋 PaperMod)
assets/css/extended/ 自訂樣式(redesign.css、custom.css)
```

## 新增內容

**寫論文筆記**(自動套用範本):
```bash
hugo new --kind papers content/posts/papers/某論文.md
```

**新增作品集**:編輯 `data/projects.yaml` 貼一筆,圖片放到 `static/images/projects/<slug>/`(檔名 `1.png`、`2.png`… 決定順序),系統會自動載入。`status` 可填 `online` / `offline` / `wip`(開發中)/ `private`(內部系統)。

**改關於我**:`content/about.md`——自介為內文,照片 / 常用工具 / 連結放在 front matter(`photo` / `tools` / `github` / `email`)。

---

Made with Hugo & PaperMod.
