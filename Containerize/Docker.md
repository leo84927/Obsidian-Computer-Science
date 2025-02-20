***Containers as a Service ( CaaS ) - 容器如同服務***
誕生於 2013 年，最初是 dotCloud 公司內部的一個 Side-Project。基於 Go 語言實作。
<h3><font color="#6A6AFF"><strong><u>基本概念</u></strong></font></h3>
1. Image (映像檔)：一個<font color="#FF0000"><strong>唯讀</strong></font>的模版，用來建立 Container。
2. Container (容器)：從 Image 建立的<font color="#FF0000"><strong>執行實例</strong></font>，Docker 利用 Container 執行應用，且每個Container 都可以被啟動、開始、停止、刪除。Container 在啟動的時候會建立一層可讀寫層作為最上層。
   ![[Container Struct.png]]
3. Repository (倉庫)：Repository 是集中存放 Image 的場所，分為 public 和 private，每個 Image 又有不同的 tag。最大的公有倉庫是 [Docker Hub](https://hub.docker.com/)。
4. Volume (資料卷)：Volume 是一個可供 Container 使用的特殊目錄
   - 可以在 Container 之間共享和重用
   - 對 Volume 的修改會立刻生效
   - 對 Volume 的更新，不會影響 Image
   - Volume 會一直存在，直到沒有 Container 使用
<h3><font color="#6A6AFF"><strong><u>Dockerfile</u></strong></font></h3>
用來建立 Image 的指令集，由一行行命令語句組成，並且支援以 # 開頭的註解行。
- FROM：指定要做為基底的 Image，當做指定作業系統。
- MAINTAINER：維護者。
- RUN：指令會在建立中執行，比如安裝一個套件，當命令較⻑時可以使用 \ 來換行。
- CMD：容器啟動後要執行的命令，每行 Dockerfile 只能有一條 CMD 命令，如果指定了多條命令，只有最後一條會被執行。
- EXPOSE：容器對外的 port。
- ADD：從本地複製單個檔案到 Image。
- COPY：從本地複製資料夾到 Image。
<h3><font color="#6A6AFF"><strong><u>基本指令</u></strong></font></h3>
1. <font color="#FFA555"><strong>docker images</strong></font>
   列出本地所有的 Image。
   - REPOSITORY：來自哪個 Repository，比如 laradock-redis。
   - TAG：Image 的標籤，比如 latest。用來標記同一個 Repository 內不同的 Image。
   - IMAGE ID：ID 號(唯一)。
   - CREATED：建立時間。
   - SIZE：Image 的大小。
2. <font color="#FFA555"><strong>docker build</strong></font> -t REPOSITORY:TAG .
   使用 Dockerfile 建立 Image，執行前需切換到 Dockerfile 所在的資料夾。
3. <font color="#FFA555"><strong>docker create/run</strong></font> -dit REPOSITORY:TAG
   建立一個新的 Container，差別在 create 只會建立不會啟動，但 run 會在建立成功後直接啟動。
   - -d：後台運行，create 不能加，因為 create 不會啟動 Container。
   - -i：attach 時鍵盤輸入會被 Container 接手。
   - -t：attach 時 Container 的螢幕會接到原來的螢幕上。
   - —name：命名 Container。
4. <font color="#FFA555"><strong>docker start/stop/restart</strong></font> 容器名稱
   啟動/停止/重啟指定的 Container。
5. <font color="#FFA555"><strong>docker exec</strong></font> -it 容器名稱 bash
   進入指定的 Container 後執行 bash。
6. docker logs 容器名稱
   查看 log
7. docker stats 容器名稱
   顯示 Container 資源
8. docker top 容器名稱
   查看 Container 中正在執行的 Process