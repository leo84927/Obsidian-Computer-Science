<h3><font color="#6A6AFF"><strong><u>Memory Leak 的定義</u></strong></font></h3>
>牛津語言(Oxford Languages)：
>A failure in a program to release discarded memory, causing impaired performance or failure. 
>程式沒能成功釋放棄用的記憶體，造成效能的損失或是執行的失敗。

>維基百科：In computer science, a memory leak is a type of resource leak that occurs when a computer program incorrectly manages memory allocations in a way that memory which is no longer needed is not released.
>在電腦科學中，記憶體洩漏是記憶體不再需要時未被釋放的不正確記憶體分配管理，是一種資源的流失。

>A memory leak reduces the performance of the computer by reducing the amount of available memory. Eventually, in the worst case, too much of the available memory may become allocated and all or part of the system or device stops working correctly, the application fails, or the system slows down vastly due to thrashing.
>Memory Leak 會讓可用的記憶體變少導致效能變差。最終，最壞的狀況是，所有可用的記憶體都被占滿，導致系統或者裝置上所有的作業都沒法正常的運作，如應用程式失效、系統變慢或者是閃退等情況。

<h3><font color="#6A6AFF"><strong><u>Memory Leak 怎麼發生的?</u></strong></font></h3>
如果有寫過 C 應該就知道 C 的 standard library (stdlib.h) 有個叫做 malloc 的 method，字面意思上就可以看得出來它是用來分配記憶體的。
那如果在使用了 malloc，程式執行完了，沒做啥其他的事情，就會 leak。那就是寫 C 時要有的習慣，寫完記得要 free()。
但問題是，人非聖賢，事無萬全。配置完記憶體用完後忘記釋放這種事情屢見不鮮...，那麼是不是有這麼一套規則，可以 malloc 完，卻不用 free 便可以自行釋放記憶體的呢？
那就是 <font color="#FF0000"><strong><u>Garbage Collection (GC)</u></strong></font>。