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
        +更新病歷(病歷ID, 資料)
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
        +更新病患資訊(資訊)
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
- ✅ 人體骨架關鍵點偵測
- ✅ 骨架特徵與變異數計算
- ✅ ADHD 機率預測與結果輸出
- ✅ 分析結果即時顯示
- ✅ 病歷管理與搜尋

以及非功能性需求：
- ✅ 效能需求（即時處理）
- ✅ 可用性需求（清晰的介面設計）
- ✅ 可維護性與擴充性（模組化架構）
- ✅ 資料儲存安全性（本地儲存）
