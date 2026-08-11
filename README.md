# website/ — n239.xyz 公開站台

**這個資料夾裡的所有東西都會被公開發佈。** 它是獨立的 git repo
（`n239-studio/n239-site`），push 到 `main` 就自動上線到 https://n239.xyz。

內部用的東西不要放進來。內部戰情室 dashboard 在
`../tools/dashboards/war-room.html`，iOS app 的原始碼與規格在 `../GlycowaveIOS/`，
上線文件在專案根目錄的 `LAUNCH_SPRINT_*.md`。這些都不屬於這裡。

## 結構

```
index.html            工作室首頁（Organization + WebSite schema）
robots.txt            爬蟲規則，含 AI 答案引擎白名單
sitemap.xml           【自動產生】由 growth/blog/build.py 產出，不要手改
llms.txt              給 AI 答案引擎讀的產品事實摘要（GEO）
assets/               工作室的 logo / OG 圖
glycowave/
  index.html          產品頁（繁中，正式版本）
  en/index.html       產品頁（英文）
  no-cgm/             支柱頁：不用 CGM 怎麼知道血糖（主定位頁）
  blog/               【自動產生】文章列表與各篇文章，不要手改
  privacy.html        隱私政策 —— App Store 審查會點
  terms.html          使用條款 —— 有訂閱就必填
  assets/
    article.css       文章頁共用樣式（支柱頁與全部文章共用）
    *.jpg / *.png     產品照片、app icon、OG 圖
CNAME                 n239.xyz
DEPLOY.md             部署與 DNS 設定
```

## 自動產生的檔案 —— 不要手改

`glycowave/blog/` 底下全部、以及 `sitemap.xml`，都是由專案外的
`growth/blog/build.py` 產生的。手改會在下次 build 時被覆蓋掉。

要改文章內容或新增文章，改 `growth/blog/articles.py`，然後：

```bash
python3 growth/blog/build.py
```

build 有四道強制檢查，沒過就不會產出任何檔案：**健康聲明必須存在、
不得出現紅線字詞、FAQ schema 必須與可見文字逐字相同、每篇必須有對照表。**
規範見 `growth/POSITIONING.md`。

## 改動時要一起顧到的事

**改目錄名稱** → 同步改 iOS 端的 `SiteLinks.swift`，否則 app 內的隱私政策
與條款連結會 404，而審查一定會點那兩個連結。

**改 FAQ 文字** → `glycowave/index.html` 與 `glycowave/en/index.html` 各有一份
`FAQPage` JSON-LD。**JSON-LD 裡的問題與答案文字，必須和頁面上看得見的
`#faq` 區塊逐字相同**——Google 明確要求 FAQ 結構化資料的內容要在頁面上
可見，兩邊不一致會被判定為不合格，rich result 直接不給。改一邊就要改另一邊。

**新增頁面** → 補進 `sitemap.xml`，並更新 `lastmod`。中英對照的頁面要三條
`hreflang`（`zh-Hant` / `en` / `x-default`），三個頁面互指要一致。

**改產品事實**（價格、免費額度、功能、平台）→ 同時更新 `llms.txt`、
兩個產品頁的 `MobileApplication` JSON-LD，以及頁面上的文案。AI 答案引擎會
引用 `llms.txt`，過期的數字會被當成現況講出去。

## 本地預覽

macOS 的 Desktop 有 TCC 保護，直接在這個目錄起 server 會被擋。
先複製到 /tmp 再起：

```bash
rsync -a --exclude .git /Users/romeye/Desktop/Tonny/App/Glycowave/website/ /tmp/n239-preview/ && python3 -m http.server 8941 --directory /tmp/n239-preview
```

## 健康聲明的紅線

產品頁、`llms.txt` 與 JSON-LD 的 `disclaimer` 都寫著同一件事：GI/GL 與餐後
反應是 **AI 估算**，不是血糖測量、不是診斷、不是醫療建議。這是 App Store
健康類別的審查重點，也是法律上的界線。做 SEO 文案時不要為了關鍵字把
「估算」寫成「測量」、把「可能」寫成「會」。
