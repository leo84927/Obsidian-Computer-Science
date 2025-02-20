<h3><font color="#6A6AFF"><strong><u>虛擬化(Virtualization)</u></strong></font></h3>
虛擬化技術想解決的核心問題是，不同電腦的作業系統與硬體資源配置不相容。
不同環境使用的作業系統不盡相同，即便同樣使用 Linux，不同版本間也存在著些微的差異。
而虛擬技術就是為了解決這種「明明在我的環境中能運作，但搬到其它地方就壞掉」的問題。
目前常見的虛擬技術有兩種，一種是以處理作業系統為單位的虛擬化技術，我們將它稱之為<font color="#FF0000"><strong>虛擬機(Virtual Machine)</strong></font>，著名的代表有 VMware。另一種是以 APP 為單位的虛擬化技術，我們將它稱為<font color="#FF0000"><strong>容器化(Containerize)</strong></font>，著名的代表有 Docker Container。
<h3><font color="#6A6AFF"><strong><u>虛擬機(VM)</u></strong></font></h3>
VM 會模擬底層的主機資源(CPU、RAM、Storage)，將資源分配給上層的應用程式使用。
在架構中有一層名為 Hypervisor 的平台，用於管理硬體資源的使用和分配。簡單來說，Hypervisor 讓使用者能夠在作業系統上(Host OS)增加另一個作業系統(Guest OS)，並且讓這兩種作業系統相互協作。
每個 VM 包含操作系統、應用程式、必要的二進製文件和系統函式庫的完整副本-容量大約為 10 GB，因此 VM 啟動的時間會較為緩慢。
<h3><font color="#6A6AFF"><strong><u>容器化(Containerize)</u></strong></font></h3>
容器化是一種在概念上相似於 VM，但在原理上略有不同的技術，其目的在於提供開發者用於測試應用程式的 sandbox。開發者將想測試的應用程式與 Dependency(相依性資源)封裝於容器進行測試。
由於容器不會模擬系統底層的基礎架構，因此需耗費的資源也相對較低，且開發者能夠在容器內配置不同的資源，因為每個容器都獨立運行且彼此之間不會互相影響。
- 容器(Container)：以應用程式(App) 為單位的虛擬化技術，App 與其 Dependency 會被封裝在 Container 中，使其可以重複利用與搬遷，Container 間彼此獨立運行，互不干擾。
- 相依性資源(Dependency)：官方是以 bins/libs 稱之，也就是<font color="#FF0000"><strong>二進位執行檔(binaries)</strong></font>與<font color="#FF0000"><strong>函數庫(libraries)</strong></font>，它們可能是 MySQL、NodeJS、.Net Framework、程式庫、DLL、編譯器...等運行App 所需要的執行環境。
- Docker Engine：類比 Hypervisor 在硬體的上層建置一個可以運行虛擬機的環境，Docker 也同樣需要在 Host OS 上建置一個可以運行 Container 的環境，Docker Engine 就是這樣的角色，將Docker Image 轉換成 Container。
  Docker Image 就如同傳統虛擬化技術將 OS 封裝成 \*.iso 映像檔(image)，Docker 也將Dependency 封裝成 Docker Image，使其可重複利用與移植。
<h3><font color="#6A6AFF"><strong><u>VM</u></strong></font> v.s. <font color="#6A6AFF"><strong><u>Containerize</u></strong></font></h3>
1. 以代表性服務為例：Docker 是<font color="#FF0000"><strong>以 App 為單位</strong></font>，VMWare 是<font color="#FF0000"><strong>以作業系統為單位</strong></font>。
2. Container 並不是 VM：Container 是一個封裝了 Dependency 與 App 的執行環境。VM 則是一個配置好 CPU、RAM 與 Storage 的作業系統。
3. Containerize 以共享 Host OS Kernel 的方式來執行 App：Container 依賴 Host OS 的<font color="#FF0000"><strong>核心(kernel)</strong></font>來運行。因此 Windows Container 必須在 Windows OS 上運行，Linux Container 必須在 Linux-base OS 上運行。
4. Container 是執行環境的實例(instance)，VM 是作業系統的實例(instance)。
5. Containerize 更面向 DevOps 的需求，Containerize 通常會與 Kubernetes 或 Docker Swarm 之類的容器調度技術整合，再配合 Drone、Jenkins 之類的 CI/CD 工具，使其可以將系統的部署工作完全自動化處理，有效降低後端維運的成本。
   而傳統的 VM 並不是處理這種議題的主要技術，雖然像 VMware 這類的工具也會有附載平衡、故障轉移、復電重啟之類的自動化維運機制，但其保障的是作業系統層級的維運需求。
6. 硬體資源的分配：Container 因為是以 App 為單位，需要的硬體資源更少，可以更細緻地使用硬體資源。在沒有 Docker 之前，我們可能為了部署一個 App 而為其配置了一台 VM，其作業系統又佔用了過多的 CPU、Storage 與 RAM。有了 Docker 之後，我們只要為一個 App 配置一個 Container 即可。
7. 應用程式的獨立性：Container 間是彼此隔離的，所以我們可以在一台 VM 下同時執行 
   NodeJS 5、NodeJS 6、NodeJS 7 等各種版本的 NodeJS Container，卻不會產生衝突。在沒有 Docker 之前，我們會為了避免衝突，為不同版本的 NodeJS 建置一台獨立的 VM，這會造成不少浪費。