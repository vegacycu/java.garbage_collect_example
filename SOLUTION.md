# 解答（SOLUTION）

> ⚠️ **請先自己判斷完、並把答案送出到 Google Classroom 的私人留言之後，再看這一份。**

## 一、連結圖

只畫「活著的 `<a href>` 連到本機 html」的連結：

```
   [GC Root]
   index.html ──────> about.html ──────> index.html
        │                 ↑
        └──────> courses.html
                     │
                     └──> index.html

   （以下從 index.html 完全走不到）

   old-news.html ──> index.html        ← 只連得出去，沒人連進來
                 └─> courses.html

   island-a.html <──> island-b.html    ← 互相連結，但外面進不來
```

`index.html` 裡雖然有一行連到 `old-news.html`，但它被包在

```html
<!-- 這則舊公告過期了，先把連結註解起來不要顯示
<p><a href="old-news.html">舊版系上公告</a></p>
-->
```

註解之中，瀏覽器不會顯示，所以**那不是一個活的引用**。

## 二、答案

| 檔案 | 判定 | 理由 |
| --- | --- | --- |
| `index.html` | **可達** | 它就是 GC Root（唯一入口） |
| `about.html` | **可達** | `index.html` 直接連到它；`courses.html` 也連到它 |
| `courses.html` | **可達** | `index.html` 直接連到它 |
| `old-news.html` | **不可達** | 唯一連到它的那一行被 HTML 註解掉了 |
| `island-a.html` | **不可達** | 只有 `island-b.html` 連到它，而 B 本身也走不到 |
| `island-b.html` | **不可達** | 只有 `island-a.html` 連到它，而 A 本身也走不到 |

### 應刪除的檔案（垃圾 / waste）

```
site/old-news.html
site/island-a.html
site/island-b.html
```

**存活 3 個，回收 3 個（3/6）。**

## 三、討論題解答

**Q1.** 不會被回收。`island-a` 與 `island-b` 互相持有引用，
兩者的引用計數永遠是 1，不會歸零 —— 這就是引用計數法著名的
**循環引用（circular reference）記憶體洩漏**問題。

可達性分析不看「被指到幾次」，而是從 **GC Roots 出發實際走一遍**；
只要從 Root 走不到，不管彼此指來指去都算垃圾。所以 A、B 都會被回收。
這也是 HotSpot JVM 採用可達性分析而非引用計數的主要原因。

**Q2.** 因為物件存活的條件是「**有人能連到我**」（被引用、可達），
不是「我能連到別人」（持有引用）。
`old-news.html` 持有指向 `index.html` 的引用，
但沒有任何存活的頁面指向它，所以它仍然是不可達物件。

在 Java 裡對應的情況：

```java
Object a = new Object();   // 這個物件本身持有其他引用
a = null;                  // 但沒有人再指向它 -> 可被回收
```

**Q3.** 會有 **1 個**檔案復活：`old-news.html`。
（`island-a` / `island-b` 仍然走不到，不會復活。）

對應到 Java 的情況是：**只要還有一條從 GC Roots 出發的引用鏈沒斷，
物件就不會被回收**。反過來說，如果程式裡忘記把不再需要的引用設為 `null`
（例如長生命週期的集合物件一直持有舊資料），
那些物件就會一直「可達」而無法回收 —— 這就是 Java 的**記憶體洩漏（memory leak）**。

## 四、常見錯誤

- 常見錯誤一：把 `old-news.html` 判成可達，因為用「搜尋 href」找到了 `index.html` 裡那一行，
  卻沒注意到它被註解掉。→ 正好用來說明「引用存在原始碼裡」與「引用實際存活」的差別。
- 常見錯誤二：把 `island-a` / `island-b` 判成可達，因為「它們互相有連結」。
  → 正好帶入引用計數 vs 可達性分析的比較。
- 常見錯誤三：把 `about.html` 或 `courses.html` 誤判成垃圾。
  → 提醒學生從 `index.html` 開始走，不要漏掉 `<nav>` 區塊裡的連結。
- 繳交方式：Google Classroom 私人留言，只要求列出**要刪除的檔名**＋一句理由，
  不必寫報告；評分重點放在「三個檔名是否找齊」與「理由是否講到不可達／沒人連進來」。
