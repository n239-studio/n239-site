# 部署 n239.xyz

工作室站台，每個 app 一個子目錄。純靜態，沒有 build step。

```
/                    工作室首頁
/glycowave/          Glycowave 官網（index / privacy / terms + assets）
CNAME                n239.xyz
```

GitHub Pages **一個自訂網域只能綁一個站台**，所以不能每個 app 各開一個 repo
去綁同一個網域。之後加第二個 app，在根目錄多開一個資料夾即可。

App 端的 `SiteLinks.siteBase` 是 `https://n239.xyz/glycowave`，
隱私政策與條款是 `siteBase + /privacy.html`、`+ /terms.html`。
**改目錄名稱就要同步改 `SiteLinks.swift`**，否則 app 裡的連結會 404 ——
而審查一定會點那兩個連結。

---

## Step 1 — 建 repo 並推上去

在 GitHub 開一個 **public** repo（例如 `n239-site`）。
Pages 免費方案要求 public。開空的，不要勾 README / .gitignore。

```bash
cd /Users/romeye/Desktop/Tonny/App/Glycowave/website
git remote add origin https://github.com/YOUR_USER/n239-site.git
git branch -M main
git push -u origin main
```

## Step 2 — 開 GitHub Pages

repo → **Settings** → **Pages**

- Source：**Deploy from a branch**
- Branch：`main`，資料夾 `/ (root)` → Save

先確認 `https://YOUR_USER.github.io/n239-site/` 開得起來，再接自訂網域。

## Step 3 — 綁自訂網域

同一頁 **Custom domain** 填 `n239.xyz` → Save。
repo 裡的 `CNAME` 檔已經是這個值，GitHub 會自動辨識。

## Step 4 — Cloudflare DNS

Cloudflare → `n239.xyz` → **DNS → Records**，新增四筆 A 記錄，Name 都填 `@`：

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

**四筆都要是「DNS only」（灰色雲朵）。** 開橘色代理的話 GitHub 沒辦法完成
憑證驗證，HTTPS 會一直發不出來。確定憑證發好之後才考慮開代理。

> 根網域同時有 MX（收信）和 A（網站）不衝突，這是正常設定。
> 加 A 記錄不會影響 `glycowave@n239.xyz` 收信。

## Step 5 — 等憑證並驗證

回 GitHub Pages 設定頁，等 **Enforce HTTPS** 可以勾（幾分鐘到一小時），勾起來。

確認這幾個都開得起來，且是 HTTPS 鎖頭正常：

- `https://n239.xyz/`
- `https://n239.xyz/glycowave/`
- `https://n239.xyz/glycowave/privacy.html`
- `https://n239.xyz/glycowave/terms.html`

後兩個是審查會點的，一定要通。

---

## App Store Connect 要填的 URL

App Information：

- **Privacy Policy URL**：`https://n239.xyz/glycowave/privacy.html`
- **Support URL**：`https://n239.xyz/glycowave/`
- **Marketing URL**（可選）：`https://n239.xyz/glycowave/`
- **Terms of Use / EULA**（有訂閱必填）：`https://n239.xyz/glycowave/terms.html`

---

## 之後改內容

改完 `git push` 就自動重新部署，一兩分鐘生效。
