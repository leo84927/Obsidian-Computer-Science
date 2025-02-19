參考資料：[https://www.dingyuqi.com/en/article/go-garbage-collection](https://www.dingyuqi.com/en/article/go-garbage-collection)
主要分為兩大類：Reference Counting、Tracing
<h3><font color="#FFA555"><strong><u>Reference Counting</u></strong></font></h3>
當一個物件被 reference，則 count 就會 +1，當物件被 release，則 count -1。
當 count 等於 0 時，則物件立刻被釋放。
Reference Counting 顯而易見、簡單、而且動作相當快，但缺點是碰到 <font color="#FF0000"><strong>Circular Reference</strong></font> 則需要手動回收否則就會发生 [[Memory Leak]]。
- Circular Reference：當 A reference 到 B，B reference 到 A 時，A B 兩個 count 都是 1，誰也不讓誰，結果就是若沒有手動回收，便會發生 Memory Leak。
<h3><font color="#FFA555"><strong><u>Tracing</u></strong></font></h3>
目前最常見的 GC 機制，並有多種不同實作方式。
基本架構是將所有物件視為一個<font color="#FF0000"><strong>樹狀結構</strong></font>，從 GC Roots 為出發點開始尋找所有被引用的物件，最終不可到達的物件即可視為垃圾被清除。
<h5><font color="#6A6AFF"><strong>Mark and Sweep (標記清除法)</strong></font></h5>
Tracing 當中最基本的 strategy，主要分為兩階段：
- 標記：從 root 開始遍歷所有物件，若該物件為可到達的 (reachable)，則將其標記。
- 清除：再次遍歷所有物件，若該物件未被標記，則將其視為垃圾清除。
缺點：執行時必須<font color="#FF0000"><strong>暫停 (Stop The World)</strong></font>、清除後會造成 [[Memory Fragmentation (記憶體碎片)]]。
<h5><font color="#6A6AFF"><strong>Mark-Sweep-Compact</strong></font></h5>
在 Mark and Sweep 之後，多一個步驟將 surviving object 移動整理在一起。可以避免 Memory Fragmentation，新的 Memory Allocation 也可以比較高效率的進行。
但需要花費額外的整理時間。
<h5><font color="#6A6AFF"><strong>Copying</strong></font></h5>
和 Mark-Sweep-Compact 的做法與目標類似，但是會<font color="#FF0000"><strong>將記憶體劃分成兩個不同的區塊</strong></font>，當下只有一個區塊供程序使用。
在 GC 時將該區塊中的 surviving 物件複製到另一個區塊，然后把已經使用過的區塊一次清理掉。
需要額外分配記憶體空間用於複製。
<h5><font color="#6A6AFF"><strong>Generational Garbage Collection (分代回收)</strong></font></h5>
其中心思想為：物件存在時間越⻑，越可能不是垃圾，應該越少去收集。這樣在回收時可以有效減少遍歷的數量，從而提高 GC 的速度。
根據物件存活的週期不同，可劃分出不同的 <font color="#FF0000"><strong>generation (世代)</strong></font>，並在記憶體中分開存放。
新生的物件多數存活週期不⻑，因此較新的世代需要較高頻率的 GC 處理。若在 GC 過程中 survive 多次的物件則可以歸到較舊的世代，以較低頻率的 GC 處理。
根據不同的世代，也可以選擇使用不同的 strategy 處理。例如 new generation 生成的垃圾較多、surviving object 較少，適合使用 Copying (surviving object 較少 -> 使用到的空間較少)。
old generation 的 surviving object 較多，可以使用 Mark-Sweep-Compact。
<h5><font color="#6A6AFF"><strong>Tri-color Marking (三色標記法)</strong></font></h5>
用三種顏色標記不同狀態的物件：
- 白色：<font color="#FF0000"><strong>尚未被掃描</strong></font>的物件。初始狀態下所有物件都是白色，掃描結束後仍為白色的會被刪除並釋放空間。
- 灰色：已被標記且正在處理隊列中的物件。
- 黑色：已被標記且完成處理的物件。
![[Tri-color Marking.gif]]
若要保證標記不出錯，需達到<font color="#FF0000"><strong>三色不變性(tri-color invariant)</strong></font>的其中一種：
- <strong><u>強三色不變性(strong tri-color invariant)</u></strong>：黑色物件<font color="#FF0000"><strong>不會</strong></font>指向白色物件，只會指向灰色物件。
- <strong><u>弱三色不變性(weak tri-color invariant)</u></strong>：黑色物件<font color="#FF0000"><strong>可以</strong></font>指向白色物件，但是此時必須包含一條從灰色物件經由多個白色物件，可以訪問到被黑色物件指向的白色物件。
  例如黑色 A 指向了白色 B，此時灰色 C 可以經由白色 D 訪問到 B，那弱三色不變性即成立。
<h5><font color="#6A6AFF"><strong>Write Barrier (寫屏障)</strong></font></h5>
- <font color="#FF0000"><strong>Insert Write Barrier</strong></font>
  每當有新的物件被 reference 或改變原有的 reference 時，就會觸發並把該物件染成灰色。該方法相對保守，它會將有可能存活的物件都標記成灰色以滿足強三色不變性。
 ````Go
 // slot 代表當前被指向的物件(B)，ptr 代表新的被指向的物件(C)
 writePointer(slot, ptr):
	 // 當觸發屏障機制時，將 C 染為灰色
	 shade(ptr)
	 *slot = ptr
 ````
  1. 將 root A 染為黑色，並將被 A 指向 的 B 染為灰色。
  2. 此時 process 修改了 A 的指標，原本指向 B 改為指向 C，<font color="#FF0000"><strong>觸發</strong></font> Insert Write Barrier 將 C 染為灰色
  3. 遍歷所有物件，並染成黑色。
  缺點：複雜度較高、性能較低
  ![[Insert Write Berrier.png]]
- <font color="#FF0000"><strong>Delete Write Barrier</strong></font>
  當舊物件的 reference 被刪除時，若舊物件為白色，將其染為灰色，以滿足弱三色不變性。
 ````Go
 // slot 代表當前被指向的物件，ptr 代表新的被指向的物件
 writePointer(slot, ptr)
	 // 第一次觸發，原本 A 指向 B 改為指向 C，將 B 染為灰色
	 // 第二次觸發，原本 B 指向 C 改為指向 null，將 C 染為灰色
     shade(*slot)
     *slot = ptr
 ````
  1. 將 root A 染為黑色，並將被 A 指向 的 B 染為灰色。
  2. 此時 process 修改了 A 的指標，原本指向 B 改為指向 C，即刪除了一個指標又新增了一個指標，<font color="#FF0000"><strong>觸發</strong></font> Delete Write Barrier，但因為 B 原本就是灰色，所以不變。
  3. 此時 process 刪除了 B 指向 C 的指標，<font color="#FF0000"><strong>觸發</strong></font> Delete Write Barrier，C 染為灰色。
  4. 遍歷所有物件，並染成黑色。
  缺點：回收精度低，一個物件即便被刪除了最後一個指向它的指標，它依舊可以活過本次循環，只能在下一輪循環被清理掉。
  ![[Delete Write Berrier.png]]
- <font color="#FF0000"><strong>Hybrid Write Barrier</strong></font>
  Insert Write Barrier 在開始時無需 STW，但結束時需要 STW 來 re-scan。
  Delete Write Barrier 開始時需要 STW 掃描 stack 記錄快照，但結束時無需 STW。
  而 Hybrid Write Barrier 則結合了兩者的優點，滿足了<font color="#FF0000"><strong>變形的</strong></font>弱三色不變性。
 ````Go
 // slot 代表當前被指向的物件，ptr 代表新的被指向的物件
 writePointer(slot, ptr)
	 // 只要 reference 有被改變，原本的和新的都要被染為灰色
	 shade(*slot)
	 shade(ptr)
	 *slot = ptr
 ````
   1. GC 開始時將 stack 上的所有物件都標記為黑色(之後不再 re-scan，無需 STW)。
   2. GC 期間，任何在 stack 上新創建的物件均為黑色。
   3. 被刪除的物件標記為灰色。
   4. 被添加的物件標記為灰色。
   PS：為了保障 stack 的運行效率，stack 上不開啟屏障。
   缺點：染色的成本變為兩倍
   解決：將需要染色的 ptr 統一寫入一個緩存，當緩存達到一定容量時，批量染色。