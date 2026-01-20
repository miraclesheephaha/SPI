# SPI Introduction
## Signal Description
1. SPI CLK：可用於common flash/TPM interface.支援17MHz,30MHz與40MHz.
2. SPI FLASH Chip Select 0：用於select the primary SPI Flash device. 此訊號除了SPI Flash之外不能用於任何其他類型設備。
   > chip select：在SPI匯流排上，master透過拉低引腳的電位來選中特定設備。
3. SPI TPM Chip Select：若TPM裝置連接至SPI介面，此訊號用於選取該裝置，不可用於其他類型裝置。  
Note：TPM可透過軟體配置(Soft Straps)設定運作於LPC或SPI介面，但系統中不允許同時存在超過一個TPM裝置  
TPM(Trusted Plaform Module，信賴平台模組)：一顆專門負責密碼編譯、儲存加密金鑰的微型安全晶片。與軟體加密不同，TPM金鑰鎖在物理硬體中，讓駭客難透過遠端軟體攻擊來竊取資料。
> 資料加密：當使用加密硬碟時，密碼金鑰會存放在TPM中。若有人偷了硬碟裝到另一台電腦上，因為沒有原本這顆TPM晶片的鑰匙，他將無法讀取資料。
4. SPI Master OUT Slave IN (MOSI)：
> master設備輸出/slave設備輸入：master模式發送數據，slave模式下接收數據

5. SPI Master IN Slave OUT (MISO)：
> master設備輸入/slave設備輸出：slave模式發送數據，master模式下接收數據
