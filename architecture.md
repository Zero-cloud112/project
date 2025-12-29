# 系統架構圖 (System Architecture Diagram)

## 智慧型兒童ADHD風險評估系統 - 系統架構

本文件描述智慧型兒童ADHD風險評估系統的整體架構設計，包含系統層次、技術堆疊、模組關係及資料流向。

---

## 系統架構總覽

```mermaid
graph TB
    subgraph "使用者介面層 (Presentation Layer)"
        UI1[錄影操作介面<br/>Video Recording UI]
        UI2[Dash 儀表板<br/>Dashboard Interface]
    end
    
    subgraph "應用層 (Application Layer)"
        APP1[錄影控制模組<br/>Recording Controller]
        APP2[分析控制模組<br/>Analysis Controller]
        APP3[視覺化控制模組<br/>Visualization Controller]
        APP4[病歷管理模組<br/>Medical Record Manager]
    end
    
    subgraph "核心業務邏輯層 (Business Logic Layer)"
        BL1[骨架偵測模組<br/>Skeleton Detection<br/>MediaPipe Pose]
        BL2[特徵計算模組<br/>Feature Calculation<br/>11 Features + Variance]
        BL3[ADHD 預測模組<br/>ADHD Prediction<br/>XGBoost Model]
        BL4[資料輸出模組<br/>Data Output]
    end
    
    subgraph "資料存取層 (Data Access Layer)"
        DA1[檔案系統管理<br/>File System Manager]
        DA2[病歷資料庫<br/>Record Database]
    end
    
    subgraph "資料儲存層 (Data Storage Layer)"
        DB1[(Session 資料夾<br/>session_YYYYMMDD_HHMMSS)]
        DB2[(病歷資料夾<br/>patient_ID_YYYYMMDD)]
        DB3[(模型檔案<br/>ML Models)]
    end
    
    subgraph "外部設備層 (External Devices)"
        EXT1[攝影機<br/>Camera]
    end
    
    %% 使用者介面層連接
    UI1 --> APP1
    UI2 --> APP3
    UI2 --> APP4
    
    %% 應用層連接
    APP1 --> BL1
    APP2 --> BL2
    APP2 --> BL3
    APP3 --> DA1
    APP4 --> DA2
    
    %% 核心業務邏輯層連接
    BL1 --> BL2
    BL2 --> BL3
    BL3 --> BL4
    BL1 --> EXT1
    
    %% 資料存取層連接
    BL4 --> DA1
    DA1 --> DB1
    DA1 --> DB2
    DA2 --> DB1
    DA2 --> DB2
    BL3 --> DB3
    
    %% 樣式設定
    classDef uiLayer fill:#e1f5ff,stroke:#01579b,stroke-width:2px
    classDef appLayer fill:#fff9c4,stroke:#f57f17,stroke-width:2px
    classDef blLayer fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef dataLayer fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px
    classDef storageLayer fill:#fce4ec,stroke:#880e4f,stroke-width:2px
    classDef extLayer fill:#e0e0e0,stroke:#424242,stroke-width:2px
    
    class UI1,UI2 uiLayer
    class APP1,APP2,APP3,APP4 appLayer
    class BL1,BL2,BL3,BL4 blLayer
    class DA1,DA2 dataLayer
    class DB1,DB2,DB3 storageLayer
    class EXT1 extLayer
```

---

## 技術堆疊 (Technology Stack)

```mermaid
graph LR
    subgraph "前端技術 (Frontend)"
        F1[Dash by Plotly<br/>互動式儀表板]
        F2[Plotly<br/>資料視覺化]
        F3[HTML/CSS<br/>介面設計]
    end
    
    subgraph "後端技術 (Backend)"
        B1[Python 3.x<br/>主要開發語言]
        B2[OpenCV<br/>影像處理]
        B3[MediaPipe<br/>姿態偵測]
    end
    
    subgraph "機器學習 (Machine Learning)"
        ML1[XGBoost<br/>ADHD 分類模型]
        ML2[NumPy<br/>數值計算]
        ML3[Pandas<br/>資料處理]
    end
    
    subgraph "資料儲存 (Data Storage)"
        D1[本地檔案系統<br/>Local File System]
        D2[CSV 檔案<br/>結構化資料]
        D3[JSON 檔案<br/>結果資料]
        D4[MP4 影片<br/>錄影檔案]
    end
    
    F1 --> B1
    F2 --> B1
    B2 --> B1
    B3 --> B1
    ML1 --> B1
    ML2 --> ML1
    ML3 --> ML1
    B1 --> D1
    D1 --> D2
    D1 --> D3
    D1 --> D4
    
    classDef frontend fill:#e3f2fd,stroke:#1565c0
    classDef backend fill:#fff3e0,stroke:#e65100
    classDef ml fill:#f3e5f5,stroke:#6a1b9a
    classDef storage fill:#e8f5e9,stroke:#2e7d32
    
    class F1,F2,F3 frontend
    class B1,B2,B3 backend
    class ML1,ML2,ML3 ml
    class D1,D2,D3,D4 storage
```

---

## 系統資料流程架構

```mermaid
flowchart TD
    Start([使用者啟動系統]) --> A[攝影機啟動]
    A --> B[即時影像擷取]
    B --> C[MediaPipe Pose<br/>骨架偵測]
    C --> D{偵測到坐姿?}
    D -->|否| B
    D -->|是| E[開始錄影<br/>60秒]
    
    E --> F[擷取骨架關鍵點<br/>33 landmarks]
    F --> G[儲存 raw_pose.csv]
    G --> H{錄影完成?}
    H -->|否| F
    H -->|是| I[儲存 record.mp4]
    
    I --> J[計算 11 維特徵<br/>角度、長度、比例]
    J --> K[滑動視窗<br/>30 frames]
    K --> L[計算特徵變異數]
    L --> M[儲存 features.csv]
    
    M --> N[載入 XGBoost 模型]
    N --> O[ADHD 機率預測]
    O --> P[儲存 result.json<br/>prob_ADHD, prob_Normal]
    
    P --> Q[Dash 儀表板載入]
    Q --> R[視覺化圖表顯示<br/>- ADHD機率折線圖<br/>- 特徵變異數圖]
    
    R --> S[病歷管理功能<br/>- 修改檔名<br/>- 搜尋病歷]
    S --> End([完成])
    
    style Start fill:#4caf50,color:#fff
    style End fill:#f44336,color:#fff
    style C fill:#2196f3,color:#fff
    style N fill:#9c27b0,color:#fff
    style Q fill:#ff9800,color:#fff
```

---

## 部署架構 (Deployment Architecture)

```mermaid
graph TB
    subgraph "本地端工作站 (Local Workstation)"
        subgraph "應用程式運行環境"
            APP[Python 應用程式<br/>ADHD Assessment System]
        end
        
        subgraph "資料儲存位置"
            FOLDER1[輸出資料夾<br/>/output/]
            SESSION[Session 資料夾<br/>session_*]
            PATIENT[病歷資料夾<br/>patient_*]
        end
        
        subgraph "硬體設備"
            CAM[USB 攝影機]
            MONITOR[顯示器]
        end
        
        APP --> FOLDER1
        FOLDER1 --> SESSION
        FOLDER1 --> PATIENT
        CAM --> APP
        APP --> MONITOR
    end
    
    subgraph "使用者"
        USER1[臨床研究人員]
        USER2[醫師]
    end
    
    USER1 --> CAM
    USER2 --> MONITOR
    
    NOTE1[註：系統完全在本地端運行<br/>無網路連線需求<br/>確保資料隱私與安全]
    
    style NOTE1 fill:#ffeb3b,stroke:#f57f17,stroke-width:2px
    style APP fill:#42a5f5,color:#fff
    style FOLDER1 fill:#66bb6a,color:#fff
```

---

## 模組互動架構

```mermaid
sequenceDiagram
    participant U as 使用者
    participant UI as 錄影介面
    participant SD as 骨架偵測模組
    participant FC as 特徵計算模組
    participant AP as ADHD預測模組
    participant DO as 資料輸出模組
    participant DASH as Dashboard
    participant MR as 病歷管理模組
    
    U->>UI: 啟動系統
    UI->>SD: 初始化攝影機
    SD->>SD: 偵測坐姿
    SD->>UI: 開始錄影
    
    loop 60秒錄影
        SD->>SD: 擷取骨架關鍵點
        SD->>DO: 儲存原始資料
    end
    
    SD->>FC: 傳送骨架資料
    FC->>FC: 計算11維特徵
    FC->>FC: 計算變異數
    FC->>DO: 儲存特徵資料
    
    FC->>AP: 傳送特徵資料
    AP->>AP: 模型預測
    AP->>DO: 儲存預測結果
    
    U->>DASH: 開啟儀表板
    DASH->>DO: 載入分析結果
    DASH->>U: 顯示視覺化圖表
    
    U->>MR: 管理病歷
    MR->>DO: 修改檔名/搜尋
    MR->>U: 回傳結果
```

---

## 安全性架構

```mermaid
graph TD
    subgraph "資料安全措施"
        S1[本地端儲存<br/>無網路傳輸]
        S2[檔案權限控制<br/>限制存取]
        S3[資料匿名化<br/>病歷號碼管理]
    end
    
    subgraph "隱私保護"
        P1[即時處理<br/>不上傳雲端]
        P2[本地加密<br/>選用功能]
        P3[存取日誌<br/>追蹤記錄]
    end
    
    subgraph "系統安全"
        SYS1[輸入驗證<br/>防止注入]
        SYS2[錯誤處理<br/>不洩漏資訊]
        SYS3[版本控制<br/>程式碼審查]
    end
    
    S1 --> P1
    S2 --> P2
    S3 --> P3
    
    P1 --> SYS1
    P2 --> SYS2
    P3 --> SYS3
    
    style S1 fill:#4caf50,color:#fff
    style P1 fill:#2196f3,color:#fff
    style SYS1 fill:#ff9800,color:#fff
```

---

## 系統架構特點

### 1. 分層架構設計
- **使用者介面層**：提供直覺的操作介面
- **應用層**：協調各模組運作
- **核心業務邏輯層**：實現主要功能
- **資料存取層**：統一資料存取介面
- **資料儲存層**：本地端檔案儲存

### 2. 模組化設計
- 各模組獨立開發、測試、維護
- 低耦合、高內聚
- 易於擴充新功能

### 3. 本地端部署
- 無需網路連線
- 資料不上傳雲端
- 保護病患隱私
- 符合醫療資料安全規範

### 4. 即時處理能力
- 即時骨架偵測（< 5秒）
- 快速分析結果（< 30秒）
- 即時視覺化顯示

### 5. 可擴充性
- 支援新增特徵項目
- 支援更新 ML 模型
- 支援整合其他分析工具

---

## 技術實作細節

### 核心技術選擇理由

| 技術 | 選擇理由 |
|------|---------|
| **Python** | 豐富的科學計算與機器學習生態系 |
| **MediaPipe Pose** | 高效的即時骨架偵測，準確度高 |
| **XGBoost** | 優秀的表格資料分類性能 |
| **Dash** | 快速建立互動式儀表板 |
| **OpenCV** | 成熟的影像處理函式庫 |
| **本地檔案系統** | 無需資料庫，簡化部署，提高隱私 |

### 效能考量

- **即時處理**：使用 GPU 加速（如可用）
- **記憶體管理**：串流處理影像，避免記憶體溢位
- **檔案 I/O**：批次寫入，減少磁碟操作
- **並行處理**：使用多執行緒處理獨立任務

---

## 與其他文件的對應關係

本系統架構圖與其他文件的關聯：

- **DFD 圖** (dfd.md)：著重資料流動，本架構圖著重系統結構
- **UML 類別圖** (hw5.md)：詳細類別設計，本架構圖呈現模組層次
- **使用案例圖** (hw3.md)：使用者視角，本架構圖呈現技術視角
- **循序圖與活動圖** (hw5.md)：動態行為，本架構圖呈現靜態結構
- **ERD 圖** (hw7.md)：資料關係，本架構圖呈現系統元件

---

## 未來擴充方向

1. **支援多攝影機**：同時從不同角度錄影分析
2. **深度學習模型**：整合 CNN/RNN 提升預測準確度
3. **雲端備份**（選用）：加密備份至私有雲
4. **多語言介面**：支援中英文切換
5. **報表自動生成**：產生 PDF 臨床報告
6. **整合其他生理訊號**：心率、眼動追蹤等

---

## 版本資訊

- **版本**：1.0
- **更新日期**：2025-12-29
- **維護者**：智慧型兒童ADHD風險評估系統開發團隊
