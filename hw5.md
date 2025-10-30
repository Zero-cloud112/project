# UML 類別圖

## 智慧型兒童 ADHD 風險評估系統

```mermaid
classDiagram
    %% 主系統類別
    class ADHDAssessmentSystem {
        -String 系統名稱
        -String 版本
        -Date 當前日期
        +初始化()
        +關閉()
        +取得系統狀態()
    }

    %% 骨架偵測模組
    class SkeletonDetectionModule {
        -Camera 攝影機
        -MediapipePose Pose偵測器
        -boolean 是否執行中
        -int 倒數計時
        +開始偵測()
        +停止偵測()
        +擷取影格()
        +取得骨架資料()
    }

    class Camera {
        -int 攝影機ID
        -int 解析度
        -int 幀率
        +開啟()
        +關閉()
        +擷取影像()
        +取得影像串流()
    }

    class MediapipePose {
        -String 模型路徑
        -float 信心閾值
        +偵測姿勢(影像)
        +擷取關鍵點(影像)
        +取得地標()
    }

    class SkeletonKeypoint {
        -int 關鍵點ID
        -String 關鍵點名稱
        -float x
        -float y
        -float z
        -float 可見度
        +取得座標()
        +取得可見度()
    }

    %% 特徵計算模組
    class FeatureCalculationModule {
        -int 視窗大小
        -int 滑動步長
        -List~SkeletonFeature~ 特徵列表
        +計算特徵(骨架資料)
        +計算變異數(特徵)
        +取得滑動視窗資料()
    }

    class SkeletonFeature {
        -String 特徵名稱
        -float 特徵值
        -String 特徵類型
        +計算角度(點1, 點2, 點3)
        +計算距離(點1, 點2)
        +計算比例(長度1, 長度2)
    }

    class FeatureVariance {
        -String 特徵名稱
        -float 變異數
        -float 平均值
        -float 標準差
        -int 視窗索引
        +計算變異數(數值列)
        +取得統計()
    }

    %% ADHD預測模組
    class ADHDPredictionModule {
        -MLModel 模型
        -String 模型路徑
        -float 閾值
        +載入模型()
        +預測(特徵)
        +取得機率()
        +儲存結果()
    }

    class MLModel {
        -String 模型類型
        -String 模型版本
        -Date 訓練日期
        +載入(模型路徑)
        +預測(輸入資料)
        +取得信心度()
    }

    class PredictionResult {
        -float ADHD機率
        -Date 時間戳記
        -String 病患ID
        -String 分析ID
        +取得機率()
        +取得時間戳記()
        +匯出為CSV()
    }

    %% 資料輸出模組
    class DataOutputModule {
        -String 輸出目錄
        -String 檔案格式
        +儲存骨架序列(資料, 檔名)
        +儲存特徵變異數(資料, 檔名)
        +儲存預測結果(結果, 檔名)
        +儲存影片(影片資料, 檔名)
        +匯出CSV(資料, 檔名)
    }

    class OutputFile {
        -String 檔名
        -String 檔案路徑
        -String 檔案類型
        -Date 建立日期
        -long 檔案大小
        +建立()
        +寫入(資料)
        +關閉()
        +取得路徑()
    }

    %% 視覺化與管理介面模組
    class VisualizationModule {
        -DashboardInterface 儀表板
        -List~Chart~ 圖表清單
        +載入最新結果()
        +顯示機率圖()
        +顯示變異數圖()
        +即時更新()
    }

    class DashboardInterface {
        -String 介面標題
        -int 重新整理頻率
        -boolean 自動更新
        +初始化()
        +重新整理()
        +載入資料(結果資料)
        +顯示圖表()
    }

    class Chart {
        -String 圖表類型
        -String 標題
        -List~DataPoint~ 資料點列表
        +建立折線圖()
        +建立長條圖()
        +更新資料(新資料)
        +渲染()
    }

    class DataPoint {
        -float x值
        -float y值
        -String 標籤
        -Date 時間戳記
        +取得座標()
        +取得標籤()
    }

    %% 病歷管理模組
    class MedicalRecordModule {
        -RecordDatabase 資料庫
        -String 儲存位置
        +建立病歷(病患ID)
        +搜尋病歷(關鍵字)
        +更新病歷ID(舊病歷ID, 新病歷ID)
        +刪除病歷(病歷ID)
        +依日期篩選(開始日期, 結束日期)
        +依關鍵字篩選(關鍵字)
    }

    class RecordDatabase {
        -String 資料庫路徑
        -List~MedicalRecord~ 紀錄清單
        +連線()
        +斷線()
        +查詢(條件)
        +插入(紀錄)
        +更新(紀錄)
        +刪除(病歷ID)
    }

    class MedicalRecord {
        -String 病歷ID
        -String 病患ID
        -String 病患姓名
        -int 年齡
        -String 性別
        -Date 記錄日期
        -List~PredictionResult~ 分析結果列表
        -List~String~ 影片檔案清單
        +取得病歷資訊()
        +新增分析結果(結果)
        +更新病歷ID(新病歷ID)
        +匯出病歷()
    }

    class Patient {
        -String 病患ID
        -String 姓名
        -int 年齡
        -String 性別
        -Date 出生日期
        -String 聯絡資訊
        +取得病患資訊()
        +更新資訊(新資訊)
    }

    %% 關聯關係
    ADHDAssessmentSystem "1" --> "1" SkeletonDetectionModule : 包含
    ADHDAssessmentSystem "1" --> "1" FeatureCalculationModule : 包含
    ADHDAssessmentSystem "1" --> "1" ADHDPredictionModule : 包含
    ADHDAssessmentSystem "1" --> "1" DataOutputModule : 包含
    ADHDAssessmentSystem "1" --> "1" VisualizationModule : 包含
    ADHDAssessmentSystem "1" --> "1" MedicalRecordModule : 包含

    SkeletonDetectionModule "1" --> "1" Camera : 使用
    SkeletonDetectionModule "1" --> "1" MediapipePose : 使用
    SkeletonDetectionModule "1" --> "*" SkeletonKeypoint : 產生

    FeatureCalculationModule "1" --> "*" SkeletonFeature : 計算
    FeatureCalculationModule "1" --> "*" FeatureVariance : 產出
    FeatureCalculationModule --> SkeletonKeypoint : 處理

    ADHDPredictionModule "1" --> "1" MLModel : 使用
    ADHDPredictionModule "1" --> "*" PredictionResult : 產生
    ADHDPredictionModule --> FeatureVariance : 分析

    DataOutputModule "1" --> "*" OutputFile : 建立
    DataOutputModule --> PredictionResult : 匯出
    DataOutputModule --> SkeletonKeypoint : 匯出
    DataOutputModule --> FeatureVariance : 匯出

    VisualizationModule "1" --> "1" DashboardInterface : 使用
    VisualizationModule "1" --> "*" Chart : 顯示
    Chart "1" --> "*" DataPoint : 包含
    VisualizationModule --> PredictionResult : 視覺化

    MedicalRecordModule "1" --> "1" RecordDatabase : 管理
    RecordDatabase "1" --> "*" MedicalRecord : 儲存
    MedicalRecord "*" --> "1" Patient : 屬於
    MedicalRecord "1" --> "*" PredictionResult : 包含
```

## 類別說明

### 主要模組

1. **ADHD評估系統 (主系統)**
   - 系統的核心控制類別，負責整合所有子模組。

2. **骨架偵測模組**
   - 負責即時影像擷取與骨架關鍵點偵測。
   - 使用 Mediapipe Pose 進行人體姿態識別。

3. **特徵計算模組**
   - 計算 11 維骨架特徵（角度、長度等）。
   - 使用滑動視窗計算特徵變異數。

4. **ADHD預測模組**
   - 載入訓練好的機器學習模型。
   - 輸出 ADHD 風險機率。

5. **資料輸出模組**
   - 儲存原始骨架序列、特徵變異數、ADHD 機率結果。
   - 匯出 CSV 檔案與錄影影片。

6. **視覺化模組**
   - 整合儀表板介面顯示即時分析結果。
   - 呈現 ADHD 機率折線圖與特徵變異數圖表。

7. **病歷管理模組**
   - 病歷資料的建立、搜尋、修改、刪除。
   - 支援依時間或關鍵字查詢。

### 支援類別

- **攝影機**: 攝影機控制。
- **Mediapipe姿態偵測**: Mediapipe 姿態偵測引擎。
- **骨架關鍵點**: 骨架關鍵點資料結構。
- **骨架特徵**: 骨架特徵（角度、距離、比例等）。
- **特徵變異數**: 特徵變異數統計。
- **機器學習模型**: 機器學習模型。
- **預測結果**: ADHD 預測結果。
- **輸出檔案**: 輸出檔案管理。
- **儀表板介面**: 儀表板互動式介面。
- **圖表**: 圖表物件。
- **資料點**: 圖表資料點。
- **紀錄資料庫**: 病歷資料庫。
- **病歷記錄**: 病歷記錄。
- **病患**: 病患資訊。

## 設計重點

1. **模組化設計**: 系統分為六大模組，各司其職，易於維護與擴充。
2. **資料流動**: 從影像擷取 → 骨架偵測 → 特徵計算 → 模型預測 → 結果視覺化。
3. **本地儲存**: 所有資料均儲存於本地端，確保個資安全。
4. **即時顯示**: 支援即時分析結果呈現，方便臨床觀察。
5. **病歷管理**: 完整的病歷管理功能，支援搜尋與查詢。

## 符合需求

此 UML 類別圖涵蓋了所有功能性需求：
- 人體骨架關鍵點偵測
- 骨架特徵與變異數計算
- ADHD 機率預測與結果輸出
- 分析結果即時顯示
- 病歷管理與搜尋

以及非功能性需求：
- 效能需求（即時處理）
- 可用性需求（清晰的介面設計）
- 可維護性與擴充性（模組化架構）
- 資料儲存安全性（本地儲存）

---

# 循序圖與活動圖

## 使用案例 1：偵測骨架關鍵點

### 循序圖 (Sequence Diagram)

```mermaid
sequenceDiagram
    actor 臨床研究人員
    participant 系統介面
    participant 骨架偵測模組
    participant 攝影機
    participant MediapipePose
    participant 資料儲存

    臨床研究人員->>系統介面: 啟動骨架偵測功能
    系統介面->>骨架偵測模組: 初始化偵測模組
    骨架偵測模組->>攝影機: 開啟攝影機
    攝影機-->>骨架偵測模組: 攝影機已就緒
    骨架偵測模組->>MediapipePose: 載入 Pose 模型
    MediapipePose-->>骨架偵測模組: 模型載入完成
    
    loop 即時偵測循環
        骨架偵測模組->>攝影機: 擷取影像幀
        攝影機-->>骨架偵測模組: 回傳影像資料
        骨架偵測模組->>MediapipePose: 偵測骨架關鍵點
        MediapipePose-->>骨架偵測模組: 回傳關鍵點座標
        骨架偵測模組->>系統介面: 顯示即時骨架追蹤
        系統介面-->>臨床研究人員: 呈現即時影像與骨架
    end
    
    臨床研究人員->>系統介面: 停止偵測
    系統介面->>骨架偵測模組: 結束偵測
    骨架偵測模組->>資料儲存: 儲存骨架關鍵點資料
    資料儲存-->>骨架偵測模組: 儲存成功
    骨架偵測模組->>攝影機: 關閉攝影機
    骨架偵測模組-->>系統介面: 偵測完成
    系統介面-->>臨床研究人員: 顯示完成訊息
```

### 活動圖 (Activity Diagram)

```mermaid
flowchart TD
    Start([開始]) --> A[啟動骨架偵測功能]
    A --> B[初始化偵測模組]
    B --> C[開啟攝影機]
    C --> D[載入 Mediapipe Pose 模型]
    D --> E{模型載入成功?}
    E -->|否| F[顯示錯誤訊息]
    F --> End1([結束])
    E -->|是| G[開始即時影像擷取]
    
    G --> H[擷取影像幀]
    H --> I[使用 Mediapipe 偵測骨架]
    I --> J{偵測到骨架?}
    J -->|是| K[擷取關鍵點座標]
    J -->|否| L[標記為無骨架]
    K --> M[顯示即時骨架追蹤]
    L --> M
    M --> N{繼續偵測?}
    N -->|是| H
    N -->|否| O[停止影像擷取]
    
    O --> P[儲存骨架關鍵點資料]
    P --> Q[關閉攝影機]
    Q --> R[釋放資源]
    R --> S[顯示完成訊息]
    S --> End2([結束])
```

---

## 使用案例 2：ADHD 機率預測與結果輸出

### 循序圖 (Sequence Diagram)

```mermaid
sequenceDiagram
    actor 臨床研究人員
    participant 系統介面
    participant ADHD預測模組
    participant 特徵計算模組
    participant ML模型
    participant 資料輸出模組
    participant 檔案系統

    臨床研究人員->>系統介面: 啟動 ADHD 預測分析
    系統介面->>ADHD預測模組: 開始預測流程
    ADHD預測模組->>ML模型: 載入訓練完成的模型
    ML模型-->>ADHD預測模組: 模型載入完成
    
    ADHD預測模組->>特徵計算模組: 請求骨架特徵資料
    特徵計算模組->>特徵計算模組: 計算 11 維骨架特徵
    特徵計算模組->>特徵計算模組: 計算特徵變異數
    特徵計算模組-->>ADHD預測模組: 回傳特徵與變異數
    
    ADHD預測模組->>ML模型: 輸入特徵資料進行預測
    ML模型->>ML模型: 執行模型推論
    ML模型-->>ADHD預測模組: 回傳 ADHD 機率
    
    ADHD預測模組->>資料輸出模組: 輸出預測結果
    資料輸出模組->>檔案系統: 儲存骨架序列資料 (CSV)
    資料輸出模組->>檔案系統: 儲存特徵變異數資料 (CSV)
    資料輸出模組->>檔案系統: 儲存 ADHD 機率結果 (CSV)
    資料輸出模組->>檔案系統: 儲存錄影影片檔
    檔案系統-->>資料輸出模組: 所有檔案儲存完成
    
    資料輸出模組-->>ADHD預測模組: 輸出完成
    ADHD預測模組-->>系統介面: 預測與輸出完成
    系統介面-->>臨床研究人員: 顯示完成訊息與檔案路徑
```

### 活動圖 (Activity Diagram)

```mermaid
flowchart TD
    Start([開始]) --> A[啟動 ADHD 預測分析]
    A --> B[載入訓練完成的 ML 模型]
    B --> C{模型載入成功?}
    C -->|否| D[顯示模型載入錯誤]
    D --> End1([結束])
    
    C -->|是| E[讀取骨架關鍵點資料]
    E --> F{資料存在?}
    F -->|否| G[顯示資料不存在錯誤]
    G --> End1
    
    F -->|是| H[計算 11 維骨架特徵]
    H --> I[使用滑動視窗計算特徵變異數]
    I --> J[正規化特徵資料]
    J --> K[輸入資料至 ML 模型]
    K --> L[執行模型推論]
    L --> M[取得 ADHD 機率預測值]
    
    M --> N[準備輸出檔案]
    N --> O[儲存原始骨架序列 CSV]
    O --> P[儲存特徵變異數 CSV]
    P --> Q[儲存 ADHD 機率結果 CSV]
    Q --> R[儲存錄影影片檔]
    
    R --> S{所有檔案儲存成功?}
    S -->|否| T[顯示儲存錯誤訊息]
    T --> End1
    S -->|是| U[產生分析報告]
    U --> V[顯示完成訊息與檔案路徑]
    V --> End2([結束])
```

---

## 使用案例 3：分析結果即時顯示

### 循序圖 (Sequence Diagram)

```mermaid
sequenceDiagram
    actor 臨床研究人員
    participant 系統介面
    participant 視覺化模組
    participant Dash儀表板
    participant 資料載入器
    participant 圖表生成器
    participant 檔案系統

    臨床研究人員->>系統介面: 開啟分析結果顯示
    系統介面->>視覺化模組: 初始化視覺化模組
    視覺化模組->>Dash儀表板: 啟動 Dash 應用程式
    Dash儀表板-->>視覺化模組: 儀表板已啟動
    
    視覺化模組->>資料載入器: 載入最新分析結果
    資料載入器->>檔案系統: 讀取 ADHD 機率 CSV
    檔案系統-->>資料載入器: 回傳機率資料
    資料載入器->>檔案系統: 讀取特徵變異數 CSV
    檔案系統-->>資料載入器: 回傳變異數資料
    資料載入器-->>視覺化模組: 回傳完整資料集
    
    視覺化模組->>圖表生成器: 生成 ADHD 機率折線圖
    圖表生成器-->>視覺化模組: 回傳機率圖表物件
    視覺化模組->>圖表生成器: 生成特徵變異數視覺化圖
    圖表生成器-->>視覺化模組: 回傳變異數圖表物件
    
    視覺化模組->>Dash儀表板: 更新儀表板顯示
    Dash儀表板-->>系統介面: 渲染完成圖表
    系統介面-->>臨床研究人員: 顯示即時分析結果
    
    loop 自動更新循環
        視覺化模組->>資料載入器: 檢查新資料
        alt 有新資料
            資料載入器->>檔案系統: 載入新分析結果
            檔案系統-->>資料載入器: 回傳新資料
            資料載入器-->>視覺化模組: 回傳更新資料
            視覺化模組->>圖表生成器: 重新生成圖表
            圖表生成器-->>視覺化模組: 回傳新圖表
            視覺化模組->>Dash儀表板: 更新顯示
            Dash儀表板-->>臨床研究人員: 即時更新畫面
        else 無新資料
            資料載入器-->>視覺化模組: 無更新
        end
    end
```

### 活動圖 (Activity Diagram)

```mermaid
flowchart TD
    Start([開始]) --> A[開啟分析結果顯示功能]
    A --> B[初始化視覺化模組]
    B --> C[啟動 Dash 儀表板應用程式]
    C --> D{儀表板啟動成功?}
    D -->|否| E[顯示啟動錯誤]
    E --> End1([結束])
    
    D -->|是| F[載入最新分析結果檔案]
    F --> G{資料檔案存在?}
    G -->|否| H[顯示無資料訊息]
    H --> I[等待新資料產生]
    I --> F
    
    G -->|是| J[讀取 ADHD 機率 CSV]
    J --> K[讀取特徵變異數 CSV]
    K --> L[解析與驗證資料格式]
    L --> M{資料格式正確?}
    M -->|否| N[顯示資料格式錯誤]
    N --> End1
    
    M -->|是| O[生成 ADHD 機率折線圖]
    O --> P[生成特徵變異數視覺化圖]
    P --> Q[配置圖表佈局]
    Q --> R[渲染圖表至儀表板]
    R --> S[顯示分析結果於介面]
    
    S --> T{啟用自動更新?}
    T -->|否| End2([結束])
    T -->|是| U[設定更新時間間隔]
    U --> V[檢查是否有新資料]
    V --> W{偵測到新資料?}
    W -->|否| X[等待下次檢查]
    X --> V
    W -->|是| Y[載入新分析結果]
    Y --> Z[更新圖表顯示]
    Z --> AA[通知使用者資料已更新]
    AA --> V
```

---

## 使用案例 4：病歷管理與搜尋

### 循序圖 (Sequence Diagram)

```mermaid
sequenceDiagram
    actor 臨床研究人員
    participant 系統介面
    participant 病歷管理模組
    participant 紀錄資料庫
    participant 搜尋引擎
    participant 檔案系統

    臨床研究人員->>系統介面: 開啟病歷管理功能
    系統介面->>病歷管理模組: 初始化病歷管理
    病歷管理模組->>紀錄資料庫: 連線至資料庫
    紀錄資料庫-->>病歷管理模組: 連線成功
    
    alt 搜尋病歷
        臨床研究人員->>系統介面: 輸入搜尋條件 (病歷號/關鍵字/日期)
        系統介面->>病歷管理模組: 傳送搜尋請求
        病歷管理模組->>搜尋引擎: 執行搜尋查詢
        搜尋引擎->>紀錄資料庫: 查詢符合條件的病歷
        紀錄資料庫-->>搜尋引擎: 回傳病歷清單
        搜尋引擎-->>病歷管理模組: 回傳搜尋結果
        病歷管理模組-->>系統介面: 顯示病歷列表
        系統介面-->>臨床研究人員: 呈現搜尋結果
    else 建立新病歷
        臨床研究人員->>系統介面: 選擇建立新病歷
        系統介面->>病歷管理模組: 請求建立病歷
        病歷管理模組->>病歷管理模組: 生成病歷 ID
        病歷管理模組->>紀錄資料庫: 插入新病歷記錄
        紀錄資料庫-->>病歷管理模組: 建立成功
        病歷管理模組->>檔案系統: 建立病歷資料夾
        檔案系統-->>病歷管理模組: 資料夾建立完成
        病歷管理模組-->>系統介面: 回傳新病歷資訊
        系統介面-->>臨床研究人員: 顯示病歷建立成功
    else 修改病歷
        臨床研究人員->>系統介面: 選擇病歷並修改病歷ID
        系統介面->>病歷管理模組: 傳送更新病歷ID請求
        病歷管理模組->>病歷管理模組: 驗證新病歷ID唯一性
        病歷管理模組->>紀錄資料庫: 更新病歷ID
        紀錄資料庫-->>病歷管理模組: 更新成功
        病歷管理模組-->>系統介面: 回傳更新結果
        系統介面-->>臨床研究人員: 顯示病歷ID修改完成
    else 刪除病歷
        臨床研究人員->>系統介面: 選擇刪除病歷
        系統介面->>臨床研究人員: 確認刪除操作
        臨床研究人員->>系統介面: 確認刪除
        系統介面->>病歷管理模組: 傳送刪除請求
        病歷管理模組->>紀錄資料庫: 刪除病歷記錄
        紀錄資料庫-->>病歷管理模組: 刪除成功
        病歷管理模組->>檔案系統: 刪除病歷資料夾
        檔案系統-->>病歷管理模組: 資料夾刪除完成
        病歷管理模組-->>系統介面: 刪除完成
        系統介面-->>臨床研究人員: 顯示刪除成功
    end
```

### 活動圖 (Activity Diagram)

```mermaid
flowchart TD
    Start([開始]) --> A[開啟病歷管理功能]
    A --> B[連線至病歷資料庫]
    B --> C{連線成功?}
    C -->|否| D[顯示連線錯誤]
    D --> End1([結束])
    
    C -->|是| E{選擇操作類型}
    
    E -->|搜尋病歷| F[輸入搜尋條件]
    F --> G{選擇搜尋方式}
    G -->|病歷號碼| H[依病歷號碼查詢]
    G -->|關鍵字| I[依關鍵字查詢]
    G -->|日期範圍| J[依日期範圍查詢]
    H --> K[執行資料庫查詢]
    I --> K
    J --> K
    K --> L{找到病歷?}
    L -->|否| M[顯示無符合結果]
    M --> End2([結束])
    L -->|是| N[顯示病歷列表]
    N --> O[選擇檢視病歷詳情]
    O --> End2
    
    E -->|建立病歷| P[輸入病患基本資料]
    P --> Q[生成唯一病歷 ID]
    Q --> R[建立病歷記錄]
    R --> S[建立病歷資料夾]
    S --> T[儲存至資料庫]
    T --> U{建立成功?}
    U -->|否| V[顯示建立失敗]
    V --> End1
    U -->|是| W[顯示建立成功訊息]
    W --> End2
    
    E -->|修改病歷| X[選擇要修改的病歷]
    X --> Y[載入病歷資料]
    Y --> Z[修改病歷ID]
    Z --> AA[驗證新病歷ID唯一性]
    AA --> AB{病歷ID有效且唯一?}
    AB -->|否| AC[顯示驗證錯誤]
    AC --> Z
    AB -->|是| AD[更新資料庫記錄]
    AD --> AE[顯示病歷ID更新成功]
    AE --> End2
    
    E -->|刪除病歷| AF[選擇要刪除的病歷]
    AF --> AG[顯示確認對話框]
    AG --> AH{確認刪除?}
    AH -->|否| End2
    AH -->|是| AI[從資料庫刪除記錄]
    AI --> AJ[刪除相關檔案與資料夾]
    AJ --> AK[顯示刪除完成]
    AK --> End2
```
