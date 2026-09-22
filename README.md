# Java 垃圾收集（Garbage Collection）小練習 ── 找出網站裡的「垃圾網頁」

> 適用課程：電子工程系「程式設計（Java）」
> 練習時間：**5～10 分鐘**
> 同時練習 **GitHub 的 fork → branch → commit → push → Pull Request** 流程
> **不用寫任何程式碼**，只要打開 HTML 檔用眼睛判斷

## 一、練習情境

`site/` 資料夾裡是一個小網站，共有 **6 個 HTML 網頁**。

這個網站**只有一個入口**：`index.html`。
訪客只能從首頁開始，一路點 `<a href="...">` 連結往下走。

> **如果有一個網頁，從首頁怎麼點都點不到它 —— 它就是垃圾（waste），可以刪掉。**

這正是 JVM 回收記憶體的做法：**可達性分析（Reachability Analysis）**。

| 這個網站 | 對應的 JVM 概念 |
| --- | --- |
| `index.html`（唯一入口） | **GC Roots** |
| 每一個 `.html` 檔案 | 堆積（Heap）裡的**物件** |
| `<a href="b.html">` | 物件之間的**引用（reference）** |
| 從首頁走不到的網頁 | **不可達物件（unreachable object）** |
| 把檔案刪掉 | **回收（Sweep）** |

## 二、判斷規則（很重要，請先讀完）

1. **只有活著的 `<a href="...">` 才算引用。**
   被 `<!-- ... -->` 註解掉的連結**不算** —— 瀏覽器根本不會顯示它，
   這等同於 Java 裡已經被設成 `null` 的引用。
2. 只計算連到**本機 `.html` 檔**的連結。
   外部網址（`https://...`）與 `mailto:` 都不算引用。
3. **「我連得出去」不代表我活著。**
   一個物件活著的條件是「**有人連得到我**」，而不是「我連得到別人」。
4. 兩個網頁**互相連結**，但從首頁都走不到 → **兩個都是垃圾**。
   （這就是 JVM 不採用「引用計數 Reference Counting」的原因）
5. `style.css` 不是這次的練習對象，不用管它。

## 三、練習步驟

### Step 1｜取得專案（約 2 分鐘）

1. 在本專案的 GitHub 頁面右上角按 **Fork**，複製一份到自己的帳號。
2. 把自己的那一份 clone 下來，並開一個新分支：

```bash
git clone https://github.com/<你的帳號>/java.garbage_collect_example.git
cd java.garbage_collect_example
git checkout -b answer-<你的學號>
```

### Step 2｜自己看 HTML，找出垃圾網頁（約 3～5 分鐘）

**方法 A（用瀏覽器）**：直接打開 `site/index.html`，從首頁開始把每個連結都點一遍，
記下你**點得到**的頁面；剩下沒被點到的就是垃圾。

**方法 B（用編輯器）**：把 6 個 HTML 檔一個一個打開，
找出裡面的 `<a href="...">`，在紙上畫出連結圖，例如：

```
index.html ──> about.html
           └─> courses.html
```

畫完後問自己：**哪些檔案的箭頭「進不來」？**

> 選用的小技巧（不是必要步驟）—— 一次列出所有連結：
> `grep -o 'href="[^"]*"' site/*.html`
> Windows PowerShell：`Select-String -Path site/*.html -Pattern 'href='`
> 注意：這個指令**連註解裡的連結也會一起列出來**，要自己回檔案裡確認那一行是不是被註解掉了。

### Step 3｜寫下答案

打開 [`WORKSHEET.md`](WORKSHEET.md)，填寫：

- 每個檔案是「可達 / 不可達」，以及你的理由
- **結論：要刪除哪些檔案**
- 三題討論題

### Step 4｜推上 GitHub 並開 Pull Request（約 2～3 分鐘）

```bash
git add WORKSHEET.md
git commit -m "作業：找出不可達的網頁（學號 xxxxxxx）"
git push -u origin answer-<你的學號>
```

然後回到 GitHub 網頁，按 **Compare & pull request** 送出 PR，
標題請寫上你的**學號與姓名**。

### Step 5｜對答案

送出 PR 之後，再打開 [`SOLUTION.md`](SOLUTION.md) 對答案。
**請務必先自己做完再看解答。**

## 四、討論題

1. `island-a.html` 和 `island-b.html` 互相連結，兩邊的「被引用次數」都是 1，不是 0。
   如果 JVM 採用**引用計數法**，這兩個檔案會被回收嗎？
   為什麼改用**可達性分析**就能回收它們？
2. `old-news.html` 裡面明明有連到首頁的連結，為什麼它還是垃圾？
3. 如果把 `index.html` 裡那段 HTML 註解**拿掉**，讓那個連結復活，
   會有幾個檔案從垃圾變成存活？這對應到 Java 裡的什麼情況？

## 五、專案結構

```
.
├── README.md          ← 練習說明（本檔案）
├── WORKSHEET.md       ← 學生填寫的答案單
├── SOLUTION.md        ← 解答（做完再看！）
└── site/              ← 練習用的小網站
    ├── index.html     ← 唯一入口（GC Root）
    ├── about.html
    ├── courses.html
    ├── old-news.html
    ├── island-a.html
    ├── island-b.html
    └── style.css      ← 不是練習對象
```
