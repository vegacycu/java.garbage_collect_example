# 答案單（Worksheet）

**學號**：
**姓名**：
**日期**：

---

## 一、可達性判斷

請在「可達？」欄填 **可達** 或 **不可達**，並簡短寫下理由
（例如：「首頁 → courses.html → 這一頁」、或「沒有任何頁面連到我」）。

| 檔案 | 可達？ | 理由 |
| --- | --- | --- |
| `index.html` | | |
| `about.html` | | |
| `courses.html` | | |
| `old-news.html` | | |
| `island-a.html` | | |
| `island-b.html` | | |

## 二、結論：應該刪除哪些檔案？

> 請把要刪除（也就是垃圾 / waste）的檔名列出來：

1.
2.
3.

**存活的檔案共 ____ 個，要刪除的檔案共 ____ 個。**

## 三、討論題

**Q1.** `island-a.html` 與 `island-b.html` 互相連結，被引用次數都不是 0。
如果 JVM 採用「引用計數法」，它們會被回收嗎？為什麼「可達性分析」可以回收它們？

> 你的回答：

**Q2.** `old-news.html` 裡面有連到首頁的連結，為什麼它還是垃圾？

> 你的回答：

**Q3.** 如果把 `index.html` 裡那段 HTML 註解拿掉，讓那個連結復活，
會有幾個檔案從垃圾變成存活？這對應到 Java 裡的什麼情況？

> 你的回答：

---

填寫完成後，請執行：

```bash
git add WORKSHEET.md
git commit -m "作業：找出不可達的網頁（學號 xxxxxxx）"
git push -u origin answer-<你的學號>
```

再到 GitHub 開一個 Pull Request，標題寫上學號與姓名。
