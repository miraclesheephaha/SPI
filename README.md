# SPI Introduction
**SPI interface 可用三組chp select signals, 分別是兩個flash設備與一個TPM設備連接至PCH**
## Signal Description
1. SPI CLK：可用於common flash/TPM interface.支援17MHz,30MHz與40MHz.
2. SPI FLASH Chip Select 0：用於select the primary SPI Flash device. 此訊號除了SPI Flash之外不能用於任何其他類型設備。
   > chip select：在SPI匯流排上，master透過拉低引腳的電位來選中特定設備。
3. SPI TPM Chip Select：若TPM裝置連接至SPI介面，此訊號用於選取該裝置，不可用於其他類型裝置。  
*Note*：TPM可透過軟體配置(Soft Straps)設定運作於LPC或SPI介面，但系統中不允許同時存在超過一個TPM裝置  
TPM(Trusted Plaform Module，信賴平台模組)：一顆專門負責密碼編譯、儲存加密金鑰的微型安全晶片。與軟體加密不同，TPM金鑰鎖在物理硬體中，讓駭客難透過遠端軟體攻擊來竊取資料。  
*Note*：透過軟體配置(Soft Strap 120,Bit 13)禁用LPC：**SPI TPM將無法運作**。因為部分SPI TPM的控制邏輯是透過LPC邏輯區塊來執行的。與透過硬體接腳選擇LPC或eSPI無關。**開發者很容易產生錯誤假設，認為既然選擇了 eSPI，就應該透過 Soft Strap 120 禁用 LPC。**  
> 資料加密：當使用加密硬碟時，密碼金鑰會存放在TPM中。若有人偷了硬碟裝到另一台電腦上，因為沒有原本這顆TPM晶片的鑰匙，他將無法讀取資料。
4. SPI Master OUT Slave IN (MOSI)：
> master設備輸出/slave設備輸入：master模式發送數據，slave模式下接收數據

5. SPI Master IN Slave OUT (MISO)：
> master設備輸入/slave設備輸出：slave模式發送數據，master模式下接收數據

## Descriptor Mode  
> Descriptor Mode是PCH所有型號(SKU)的必要條件，系統不支援Non-Descriptor Mode。
> **SKU (Stock Keeping Unit)** 是指不同等級的的晶片組(消費級、商用級)，無論用哪種等級的南橋，全部都必須使用Descriptor Mode
## SPI Flash Regions
**Flash Descriptor 與 Intel Manegement Engine是必須具備的區域**  
> Flash Descriptor像整個Flash的地圖。PCH硬體在電力開啟後的幾微秒內，讀取Region0且必須位於the first sector of device0 (offset 0).為了要讓PCH知道Flash的容量、頻率等。  
> Device 0(第一個晶片)：接兩顆Flash (CS0與CS1)，Descriptor必須放在CS0的最開頭。   
> Intel ME Region在現代Intel架構中，ME是系統啟動的先遣部隊，必須比CPU更早開始運作。  
> Regions can extend across multiple components, but must be contiguous.

**Flash區域大小**  
SPI Flash的空間需求因平台的配置而異，Flash空間的實際消耗量取決於Flash元件的Erase Granularity，以及平台對Intel ME與BIOS區域的須求。  
Intel ME Region包含支援Intel主動管理技術(AMT)及其他Intel ME功能的韌體。    
> Erase Granularity：SPI Flash最小的抹除單位通常是4KB (Sector)。即便資料量只有 100 位元組，它也必須佔用一個完整的 4 KB 區塊，因為 SPI Flash 無法單獨抹除比這更小的單位。  
> GbE(2個4KB區塊)：Intel 整合式網卡區域（GbE Region）通常需要 8 KB，分為主要（Main）與備援（Backup）存儲空間。  
> Intel ME Region：空間消耗大。視功能而定（例如 1.5 MB 的輕量版或是 5 MB 以上的企業版 AMT），此區域的大小變動最大。  
> Intel Active Management Technology (AMT)：這是 Intel 針對商用電腦設計的遠端管理技術，包含在 ME 韌體中。

**Flash Descriptor Regions**  
* Flash Signature用於選擇Descriptor mode
