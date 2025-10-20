+++
title = 'Git 瘦身筆記：移除歷史記錄中的大型檔案'
date = 2025-10-20T15:52:51+08:00
draft = false
+++

> 將 Git 專案的大小從 **1.3 GB** 成功縮減，並將清理後的歷史記錄同步到遠端伺服器。
<!--more-->
---
### 🎯 專案目標

將 Git 專案的大小從 **1.3 GB** 成功縮減，並將清理後的歷史記錄同步到遠端伺服器。

### 🚨 問題發現與檢查流程

| 階段 | 動作 | 發現/結論 |
| :--- | :--- | :--- |
| **起始問題** | git clone 後發現專案大小高達 1.4 GB，遠超程式碼應有的大小。 | **初步診斷：** 專案體積異常巨大，懷疑 Git 歷史記錄有問題。|
| **檢查大小** | 執行 du -sh . | 確認專案總體積為 1.3G。 |
| **定位根源** | 執行 du -h -d 1 . 檢查子目錄大小。 | **發現：** 工作目錄下的檔案（如 ./compiler）極小，推斷 **.git 資料夾**（歷史記錄）佔了絕大部分空間。 |
| **追溯歷史** | 透過檢查提交紀錄，證實了猜測。 | **問題確認：** 曾在早期提交 (upload project Commit) 中，**誤將大型檔案加入版本控制**（即「曾經上傳過很大的檔案」）。|
| **鎖定元兇** | 檢查該 Commit 的內容。 | **目標檔案：** archived/codehelper.tar 和 archived/gcc-api.tar。|

**結論：** 必須透過重寫歷史記錄，將這兩個檔案從所有版本中**永久刪除**，才能達到瘦身的目的。

### 🛠️ 預期處理方法：重寫歷史記錄

* **選用工具：** **[BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)** (高效且安全地重寫 Git 歷史)。
* **操作策略：** 在專案的**鏡像克隆**上進行操作，避免破壞當前的工作目錄。

### ⚙️ 處理過程與指令紀錄

| 步驟 | 說明 | 執行指令 (路徑已識別化) |
| :--- | :--- | :--- |
| **前提準備** | **安裝 Java 和 BFG** | 確保 Java 環境和 bfg-1.15.0.jar 已準備好。|
| **1. 鏡像克隆** | 建立鏡像副本並進入操作目錄。 | `cd /Users/.../Repo`<br>`git clone --mirror codehelper codehelper-cleaner.git`<br>`cd codehelper-cleaner.git` |
| **2. 執行 BFG** | 使用 BFG 從所有歷史記錄中，刪除兩個大 .tar 檔案。 **(注意修正指令格式)** | `java -jar [BFG 路徑] --delete-files "{codehelper.tar,gcc-api.tar}" .` |
| **3. 執行 GC** | 清理 Git 殘留日誌，並強制執行垃圾回收，**釋放磁碟空間**。 | `git reflog expire --expire=now --all`<br>`git gc --prune=now --aggressive` |
| **4. 設定遠端** | **修正錯誤：** 移除鏡像上指向本地的遠端設定，改設為真正的學校伺服器 URL。 | `git remote rm origin`<br>`git remote add origin https://.../codehelper.git` |
| **5. 強制推送** | 將清理後的歷史記錄**強制推送到遠端伺服器**。 | `git push --force --all`<br>`git push --force --tags` |
| **6. 本地清理** | 刪除舊的專案和鏡像，重新克隆乾淨的版本。 | `cd ..`<br>`rm -rf codehelper`<br>`rm -rf codehelper-cleaner.git`<br>`git clone https://.../codehelper.git codehelper`|

### ✅ 處理總結

* **最終結果：** 成功從 Git 歷史記錄中永久移除了兩個大型 .tar 檔案，並在遠端伺服器上覆蓋了舊的歷史記錄。
* **效益：** 新的 clone 大小已大幅縮減（預期降至數 MB），解決了 Git 操作緩慢的問題。
* **下一步：** 必須確保所有協作者刪除他們本地的舊副本，並從遠端伺服器重新克隆新、瘦身後的專案。