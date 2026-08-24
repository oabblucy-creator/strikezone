# strikezone
大帥哥2

## 🎯 烽火地帶（撤離模式）

線上玩：https://oabblucy-creator.github.io/strikezone/
（第二個世界：https://oabblucy-creator.github.io/strikezone/polaris-field/ ）

三個模式：**烽火地帶**（主打）／合作生存（原本的波次）／單挑對戰。
**五把刀和所有槍枝皮膚全部免費，一開始就解鎖。**

### 怎麼玩烽火地帶
1. 大廳選裝備（護甲／頭盔／背包／醫療包），按 **🎯 進入戰區**。裝備成本會先從哈夫幣扣掉。
2. 走進房子，**看著箱子按住 F** 搜刮。搜到的東西進背包，背包有格數上限。
3. 場上一直會刷幽靈兵，打死會掉戰利品包（也能搜）。
4. 地圖四個角有**撤離點**（綠色光柱，畫面上會顯示距離）：站進去撐 **8 秒**就撤離成功。
5. **撤離成功**＝物資進倉庫、裝備原樣拿回來。**陣亡或 6 分鐘到**＝背包和身上裝備全部沒收。
6. 倉庫的東西賣掉就變哈夫幣，拿去買更好的裝備。

### 按鍵
| 按鍵 | 動作 |
|---|---|
| 按住 F | 搜刮箱子（沒對著箱子時 F＝檢視武器） |
| Tab | 開背包（點物品可以丟掉騰格子） |
| H | 用醫療包 |
| WASD／Shift／空白／Ctrl | 移動／衝刺／跳／蹲（跑步中 Ctrl＝滑鏟） |
| 左鍵／右鍵 | 射擊／瞄準 |
| 1 / 2 / 3（或滾輪、Q） | 步槍／狙擊／你帶的刀 |
| C / V / G | 幹員技能（威龍、蜂醫） |
| T | 噴漆 |

手機：左下搖桿移動、右側拖曳轉視角，右邊有 **搜刮** 和 **背包** 按鈕。

### 地圖
西北工廠／東北民宅／西邊醫療站／中央塔樓（高價物資）／東邊碼頭倉庫／東南商店街／南方農舍／西南隧道口
—— 房子都能走進去，一共 53 個可搜刮的容器。
撤離點：北方公路、東側碼頭、西南隧道、南方農場。

## ☁ 雲端存檔（綁 Google 帳號）

哈夫幣和倉庫預設存在**瀏覽器本機**，換一台裝置或清快取就沒了。
綁 Google 帳號之後，進度會存到雲端，換手機、換電腦都還在，**而且錢花掉了就是花掉了，重整回不來**。

用的是 **Firebase**（Google 自己的服務）：免費方案 Spark，**不用信用卡**，
每天 5 萬次讀取／2 萬次寫入，這個遊戲用不到十分之一。

> ⚠️ 建立 Firebase 專案需要一個「一般」的 Google 帳號。
> 如果你用的是家長監護（Family Link）的兒童帳號，可能會建不起來，要請家長用他們的帳號幫忙建一次。

### 設定步驟（只要做一次）

1. 開 https://console.firebase.google.com/ ，用 Google 帳號登入。
2. **新增專案** → 名字打 `strikezone` → Google Analytics 可以**關掉**（不需要）→ 建立。
3. 左邊選單 **建構 → Authentication → 開始使用 → Sign-in method → Google → 啟用 → 儲存**。
4. 左邊選單 **建構 → Firestore Database → 建立資料庫** → 選 **以正式版模式啟動** →
   位置選 **asia-east1**（台灣最近）→ 啟用。
5. Firestore 上面的 **規則** 分頁，把內容整個換成下面這段，按 **發布**：

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /players/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```

   這段的意思是：**每個人只能讀寫自己的存檔**，別人的動不了。

6. 左上角齒輪 **專案設定** → 往下拉到「您的應用程式」→ 點 **`</>`（網頁）** →
   暱稱隨便打 → **註冊應用程式** → 會出現一段 `firebaseConfig`，裡面有四個值要用。
7. **Authentication → Settings → 授權網域 → 新增網域**，加入
   `oabblucy-creator.github.io`（`localhost` 本來就有，不用加）。
8. 打開 `index.html`，搜尋 `const FIREBASE`（在 `<script>` 開頭附近），
   把第 6 步那四個值貼進去：

   ```js
   const FIREBASE = {
     apiKey:     "AIza....",
     authDomain: "strikezone-xxxx.firebaseapp.com",
     projectId:  "strikezone-xxxx",
     appId:      "1:123456789:web:abcdef"
   };
   ```

9. 存檔 → `git commit` → `git push`。大廳就會出現「用 Google 帳號登入」按鈕。

> `apiKey` 貼在網頁裡被看到是**正常的**，Firebase 本來就這樣設計 ——
> 真正的保護是第 5 步那段規則。所以規則一定要照上面貼，不要用「測試模式」放著不管。

### 綁定之後會怎樣
- 大廳最上面有 **用 Google 帳號登入** 按鈕，登入後顯示 `👤 你的名字`＋登出。
- **第一次登入**：這台裝置目前的進度會自動搬到你的帳號。
- **之後登入**：一律以**帳號裡的存檔**為準（所以在別台亂改也沒用）。
- 出擊扣裝備錢、結算、賣倉庫都會**立刻**寫上雲端。
- 沒登入或雲端連不上時，遊戲照樣能玩，只是進度存在這台裝置。

### 檔案
- `index.html` — 全部程式都在這一檔（3D 場景、槍刀模型、動畫、連線、烽火地帶系統）
- `polaris-field/` — 第二個世界（北極星刀的夜間空地場景）

### 除錯把手（瀏覽器 console）
`window.__sz.ops`：`raid()` 開一局 / `loot(n)` 直接搜 n 個箱子 / `tp(x,z)` 傳送 /
`toExfil(i)` 傳到第 i 個撤離點 / `time(t)` 設定經過秒數 / `bagUI(true)` 開背包 /
`win()` `lose()` 直接結算 / `setHaff(n)` 改哈夫幣 / `s` 看局內狀態
