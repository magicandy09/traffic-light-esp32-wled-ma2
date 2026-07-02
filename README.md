hi im a student from taiwan and i wanna to share my tiny DIY project, i was majored in theatre lighting design,and im not a pro in coding and engineering,thus i might made some inefficient solution haha.

# 🚦 專案日誌：基於 ESP32 與 WLED 實現 MA2 控制的 512 點陣智慧紅綠燈系統
# Dev Log: Implementing a 512-Pixel Smart Traffic Light System via ESP32, WLED, and grandMA2

> **💡 Welcome! This is a bilingual development log (中文 / English). Scroll down for the Chinese version! ⚠️ Friendly Disclaimer: This project is simply a passionate DIY, layman-style build share and research log. Friendly technical insights, suggestions, and creative discussions are highly welcome! However, toxic, irrational, or disrespectful comments will not be tolerated. Let's keep the maker community positive and constructive!**


https://github.com/user-attachments/assets/b5a4a115-055f-429c-a387-ce6e2be2492b



### 01. Project Genesis: Why Video Projection Fell Short
This project was originated from a theatrical production directed by a colleague, which required a physical traffic light capable of real-time, high-speed interaction with live music cues. 

Initially, the production team considered using **Video Projection** to display the traffic light. However, we hit two major technical bottlenecks:
* **Real-Time On-Site Programming Flexibility:** Once a video asset is rendered, adjusting its color temperature and specific behaviors on the fly during rehearsals is extremely inefficient.
* **Absolute BPM Synchronization:** Multiple cues required the flashing frequency to **accelerate dynamically in perfect sync with the music's live tempo (BPM)**. Achieving such instantaneous, adaptive scaling within traditional playback software lacks granular flexibility.

To overcome these limits, I decided to return to the core of **Physical Lighting Control**, engineering a high-density addressable LED matrix.

### 02. Hardware Selection & The DMX Channel Crisis
To achieve crisp visual patterns, I selected two **WS2812B 16×16 RGB LED Matrix Panels**, totaling **512 individually addressable pixels**.

Upon seeing the hardware, an industry programmer warned me: **"This is impossible in a traditional architecture. A single 16×16 matrix contains 256 pixels. Multiplied by 3 channels (RGB), that is 768 DMX channels—instantly shattering the threshold of a single traditional DMX Universe (512 channels)!"**

Daisy-chaining both panels meant handling a massive stream of 1,536 channels (3 full DMX Universes), which would result in severe data latency using traditional serial hardware decoders.

#### 💡 The Architectural Breakthrough: Lighting over IP via ESP32 & WLED
To resolve this bottleneck, I spent nearly two months researching **Lighting over IP (LoIP)** logic, moving away from traditional serial DMX architectures:
* **Hardware Core:** Employed an **ESP32 NodeMCU** development board as the wireless network receiver.
* **Software Ecosystem:** Flashed **WLED** firmware to harness network bandwidth capable of high-throughput pixel ingestion.
* **Spatial Matrix Mapping:** While both panels shared a single Data line, I utilized the WLED backend to custom-configure the **spatial orientation, matrix geometry, and starting pixel offsets**, successfully achieving precise 2D coordinate mapping for all 512 pixels.

### 03. Control Topology: grandMA2 ➔ Wi-Fi Router ➔ ESP32 (Art-Net)
For the control front-end, I utilized the industry-standard **grandMA2 (MA2) console**. I implemented a **Frame-by-Step Animation** logic to program the traffic light sequences:
* I programmed `0-99` countdown cues and linked them sequentially via the console's internal **Link commands**.
* The dynamic green light sequences were also linked frame-by-frame, giving me absolute freedom to create **instantaneous acceleration and deceleration effects** on the fly.
* I also integrated grandMA2's native **Effect Engine** for smoother fluid color transitions.


https://github.com/user-attachments/assets/12d1d597-f6f5-4bb7-9616-7187cd8701a9


The MA2 broadcasted **Art-Net streams** over a WLAN managed by a router (Network Topology). The ESP32 ingested the Art-Net packets and decoded the payload into an NRZ data stream for the WS2812B chips. For field-operation diagnostics, I integrated an **I2C OLED Display** onto the ESP32 to monitor live Wi-Fi SSID, IP address, and packet reception rate (Telemetry).

### 04. Advanced Troubleshooting: The Ethernet Hurdle & Custom Code
Driven by the desire for absolute signal stability, I attempted to migrate the system from Wi-Fi to a **wired Ethernet architecture** and code custom effects:
1. **Arduino IDE & Adafruit Libraries:** I successfully wrote custom C++ code to light up the pixels. However, I found that coding complex fluid animations from scratch consumed too many CPU cycles and extended the development cycle. To optimize project delivery, I pivoted to WLED, delegating effect generation to grandMA2's robust engine.
2. **The Wired Ethernet Challenge:** I procured two different Ethernet expansion modules (with different **PHY Chips**) to map static IPs via Arduino IDE. Due to persistent hardware initialization conflicts, the modules failed to maintain a stable connection. Facing tight deadlines, I made the executive decision to **roll back to the native Wi-Fi architecture**, ensuring on-time project delivery.
<img width="1536" height="2048" alt="5ac41f6e-5f44-408d-a138-691a0af546e1" src="https://github.com/user-attachments/assets/dea1015b-3c9f-4acb-b6cb-fceeef37618e" />

### 05. Future Scalability in Commercial & Architectural Lighting
This experimental prototype demonstrates massive potential within **Architectural & Facade Lighting**:
* **Innovative Capture-Playback Workflow:** Utilize grandMA2's world-class Effect Engine to rapidly program sophisticated pixel maps, then capture the raw Art-Net streams using a standalone recorder (e.g., ENTTEC) for 24/7 cyclical playback on site, eliminating the need to leave an expensive console permanently on-site.
* **Current Architectural Constraints (Parameter Constraints):** A single RGB pixel consumes 4 parameters on MA2. This project’s modest 512 pixels consumed $512 \times 4 = 2,048$ parameters, instantly cannibalizing exactly half of a grandMA2 Command Wing's total system capacity (4,096 parameters). Bypassing these console parameter locks while preserving programming aesthetics—likely via **Media Servers or Pixel Mappers**—will be my next phase of research.

### 06. Some Tryouts  



https://github.com/user-attachments/assets/28414f0b-31dd-4fe4-a4fd-fac7f0d1f10e



https://github.com/user-attachments/assets/e80a51e2-0f14-46f3-a18c-7400c244c820



https://github.com/user-attachments/assets/60920a80-cc98-4d82-b9f0-f903577150c2


---

* ### 01. 創作動機與挑戰：為什麼影像設計無法解決這場戲？
這個專案源於朋友編導的一齣戲劇，劇中需要一個能跟現場音樂極速互動、動態變化的「紅綠燈」。

一開始，團隊曾考慮使用**投影影像（Video Projection）**來解決，但很快就遇到了兩個技術瓶頸：
* **即時修改的彈性：** 投影的影像一旦渲染完成，很難在現場根據排練即時調整顏色、亮度與動態範圍。
* **與音樂拍點（BPM）的絕對同步：** 劇中有多個環節需要紅綠燈的閃爍速度**隨著音樂的拍點（BPM）即時加速**。在傳統影像軟體中，要做到這種即時、隨機且無縫的變速與對點，難度極高且缺乏彈性。

因此，我決定回歸**實體燈光控制（Lighting Control）**的本質，利用硬體點陣燈具來達成完美的即時變速與對點。

### 02. 硬體選型與 DMX 頻寬危機
在硬體實作上，為了達到高密度的視覺效果，我放棄了傳統燈條手動做 Z 字型焊接的費時作法，直接採用了兩塊海外採購的 **WS2812B 16×16 RGB 點陣燈板**，總計 **512 個可定址像素（Pixels）**。

此時，業界做燈光的朋友向我提出了警告：**「這在傳統系統裡不可行。光是單單一塊 16×16 的燈板就有 256 個 Pixel，乘上 RGB 3 通道，就高達 768 個 DMX 頻道。這直接衝破了傳統單一 DMX Universe（512 頻道）的物理極限！」**

如果兩塊燈板串接，資料量高達 1,536 個頻道（等同於要吃掉整整 3 個 DMX Universes）。

#### 💡 我的跨界解法：引入 ESP32 與 WLED 網路照明架構
為了解決這個頻寬危機，我花了近兩個月的時間，從零研讀網路照明（Lighting over IP）的底層邏輯，跳脫傳統序列訊號（Serial DMX）的思維，自己規劃了一套網路控制架構：
* **硬體核心：** 使用 **ESP32 NodeMCU** 作為網路訊號接收器與微控制器。
* **軟體系統：** 刷入 **WLED** 系統，利用網路協定的高頻寬來吞吐大資料量（Data Ingestion）。
* **空間矩陣定位：** 兩塊燈板在實體線路上只靠一條 Data 線串接，但我透過 WLED 後台，自行定義了這兩塊面板的**空間走向、矩陣幾何（Matrix Mapping）與起始位置**，在軟體層面成功完成了 512 顆燈珠的空間定址與畫面重組。

### 03. 控制鏈建立：grandMA2 ➔ WiFi Router ➔ ESP32 (Art-Net)
控制端我使用業界標準的 **grandMA2 控制台**。我用了**逐幀動畫（Frame-by-Step Animation）**的邏輯來編寫（Program）這套紅綠燈的 Cue：
* 我寫了 `0-99` 正數和倒數的 Cue，並用控台內部的 **Link 指令**將它們串聯在一起。
* 綠燈的動態畫面，也是透過每一幀（Frame）的獨立畫面互相 Link。這樣一來，我可以在控台上極度自由且即時地做出**加速與減速效果**。
* 同時，我也融入了幾個 grandMA2 內建的 **Effect（特效引擎）**，讓整體色彩動態更流暢。

MA2 開啟 **Art-Net 輸出**，透過無線路由器（Router）發送廣播訊號（Network Topology）。ESP32 透過無線網路接收 Art-Net 訊號包，並即時將訊號解碼為 WS2812B 的 **NRZ Data Stream**。

為了優化操作體驗，我另外連接了一塊 **OLED 小螢幕（I2C 介面）**至 ESP32，即時顯示當前的 Wi-Fi 連線狀態、IP 位址與訊號接收率（Telemetry）。

### 04. 進階 Debug 紀錄：乙太有線網路與代碼特效的挑戰
在開發過程中，我挑戰了兩個更進階的底層技術：
1. **嘗試使用 Arduino IDE 與 Adafruit 函式庫自製特效：** 雖然成功點亮了燈具，但我發現若要實現複雜的漸層、動態加速及即時對點，純代碼開發會耗費大量微控制器的運算資源。為了提高專案的「交付效率」，我果斷改用 WLED 架構，將特效交給 grandMA2 去運算。
2. **有線乙太網路（Ethernet）的卡關：** 我先後採購了兩款不同的乙太網路擴充模組（搭載不同的 **PHY Chips**），試圖與 ESP32 進行硬體接線，試圖固定 IP。然而，在經過多次反覆測試設定後，系統依舊無法穩定識別模組。考量到演出的時間壓力，我果斷決定**退回使用原本的無線 Wi-Fi 方案**，確保專案如期上線。

### 05. 商業與建築照明的未來發展性
這次的實驗讓我看到這個架構在**建築照明（Architectural Lighting）**上的巨大潛力。
* **創新工作流（Capture-Playback Workflow）：** 利用 grandMA2 強大的 Effect Engine 進行即時編排，再透過 DMX/Art-Net 錄製盒（如 ENTTEC 等設備）將這些高階特效錄製下來，最後在案場進行 24 小時循環播放。
* **現階段的技術疑慮（Parameter Constraints）：** MA2 一個 RGB Pixel 需要佔用 4 個參數。本專案 512 個 Pixel 就需要消耗 $512 \times 4 = 2,048$ 個參數，直接吃掉一台 grandMA2 Command Wing 一半以上的輸出極限（4,096 參數）。如果擴大到整棟大樓的外牆，參數授權成本將會是天文數字。下一步的研發重點，將是尋找如何在「維持 MA2 的編排美感」與「降低參數授權成本」之間取得平衡。

---
