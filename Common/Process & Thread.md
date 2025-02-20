<h3><font color="#6A6AFF"><strong><u>Program</u></strong></font></h3>
程式碼 (code) 的集合，也就是還<font color="#FF0000"><strong>尚未 load 進記憶體的程式碼</strong></font>。
可以想像成設計一座工廠的<font color="#FF0000"><strong>藍圖</strong></font>就是 Program。
- 相同 Program 的 Process 可以同時存在多個，也就是可以用同一張設計圖建立多座工廠。
<h3><font color="#6A6AFF"><strong><u>Process</u></strong></font></h3>
已經執行並且 <font color="#FF0000"><strong>load 到記憶體中的 Program</strong></font>。
若 Program 是工廠藍圖，Process 就是照著設計藍圖所完成的<font color="#FF0000"><strong>實體工廠</strong></font>。
Process 中的每一行程式碼隨時都有可能被 CPU 執行，點開應用程式就是將 Program 活化成 Process，在活動監控器 (mac) 或 jobs (linux) 中看到 <font color="#FF0000"><strong>PID</strong></font>，就是執行中的 Process ID。
- Process 是電腦中已執行 Program 的實體。
- <font color="#FFA555"><strong>每一個 Process 是互相獨立的</strong></font>。
- Process 本身<font color="#FF0000"><strong>不是基本執行單位</strong></font>，而是 Thread (執行緒) 的容器。
- Process 需要一些資源才能完成工作，如 CPU、記憶體、檔案以及I/O裝置。
- 在多功作業系統 (Multitasking OS) 中，可以同時執行數個 Process，然而一個 CPU 一次只能執行一個 Process (因此才有現在的多核處理器)，而 Process 的運行量總量 >= CPU 的總量，又 Process 會佔用記憶體，因此<font color="#FF0000"><strong>排程(Scheduling)</strong></font> 、有效管理記憶體則是 OS 所關注的事。
<h3><font color="#6A6AFF"><strong><u>Thread</u></strong></font></h3>
在同一個 Process 中會有很多個 Thread，每一個 Thread 負責某一項功能。
連結 Program 、Process 的想像，Thread 就是工廠內的<font color="#FF0000"><strong>工人</strong></font>，確保工廠的每項功能，並且共享工廠內的每一項資源。
- Thread 是系統處理工作的基本單元。
- 同一個 Process 會同時存在多個 Thread。
- 一個 Process 底下的 Thread 共享資源，如記憶體、全域變數 (Global Variable)等，不同的 Process 則否。
- 在<font color="#FF0000"><strong>多執行緒中 (Multi-threading)</strong></font>，兩個 Thread 若同時存取或改變全域變數，則可能發生同步 (Synchronization) 問題。若 Thread 之間互搶資源，則可能產生 [[Deadlock]]。