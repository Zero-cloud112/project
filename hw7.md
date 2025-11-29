```mermaid
erDiagram
    
    SESSION {
        string session_id PK "資料夾名稱/會話 ID"
        string timestamp "開始時間戳"
        string patient_id "病歷號碼 (可為空)"
    }

    RAW_POSE {
        string session_id FK
        int frame PK "幀索引"
        %% 註釋：包含 33 個 MediaPipe 地標的 
        %% X, Y, Visibility 數據 (共 99 個欄位)
    }

    FEATURES {
        string session_id FK
        int window_index PK "滑動窗口的索引 (用於時間序列)"
        float left_hip_length "左髖長度變異數"
        float left_hip_angle "左髖角度變異數"
        float right_hip_length "右髖長度變異數"
        float right_hip_angle "右髖角度變異數"
        float left_shoulder_length "左肩長度變異數"
        float left_shoulder_angle "左肩角度變異數"
        float right_shoulder_length "右肩長度變異數"
        float right_shoulder_angle "右肩角度變異數"
        float trunk_angle "軀幹角度變異數"
        float right_thigh_length "右大腿長度變異數"
        float right_thigh_angle "右大腿角度變異數"
    }

    RESULT {
        string session_id PK "會話ID (與SESSION關聯)"
        float prob_ADHD "ADHD 預測機率"
        float prob_Normal "Normal 預測機率"
    }

    SESSION ||--|{ RAW_POSE : generates
    SESSION ||--|{ FEATURES : generates
    SESSION ||--|| RESULT : summarizes
```

###  ERD　說明

本實體關係圖 (ERD) 描述了 **ADHD 自動分析系統**的數據結構。其核心在於捕捉坐姿狀態下的運動特徵變異數並進行分類。

---

#### 1. 核心實體與鍵值

| 實體 | 說明 | 關鍵鍵值 |
| :--- | :--- | :--- |
| **SESSION (會話)** | 代表單次完整的錄影與分析過程。 | **PK**：`session_id` (作為所有生成數據的唯一識別符) |
| **RESULT (結果)** | 儲存模型的最終預測機率 (`prob_ADHD`, `prob_Normal`)。 | **PK**：`session_id`。此主鍵確保每筆 $\text{SESSION}$ 只有一筆 $\text{RESULT}$ 記錄，代表一個嚴格的 **1:1 關係**。 |

#### 2. 數據細節與特徵提取

* **RAW\_POSE (原始骨架數據)**
    * **說明**：儲存從攝影機捕捉的每一幀 (`frame`) $\text{MediaPipe}$ 骨架點的原始坐標。數據可用於回溯分析或重新計算。
    * **複合主鍵**：(`session_id`, `frame`)

* **FEATURES (特徵變異數)**
    * **說明**：儲存對原始特徵進行**滑動窗口**計算後得到的**變異數 ($\text{Variance}$)**。這些變異數是用於 $\text{XGBoost}$ 模型輸入的特徵。
    * **複合主鍵**：(`session_id`, `window_index`) 用於標識每個 $\text{SESSION}$ 中不同時間點（窗口）的特徵變化。

#### 3. 關係說明

* **SESSION generates RAW\_POSE / FEATURES (1:M)**
    * 一個會話會產生多幀原始數據和多個滑動窗口的特徵數據。
* **SESSION summarizes RESULT (1:1)**
    * 一個會話只會產生一個最終的預測結果。
