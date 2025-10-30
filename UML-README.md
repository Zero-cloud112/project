# UML 類別圖說明文件

## 檔案說明

本專案包含以下 UML 類別圖檔案：

1. **uml-class-diagram.md** - Mermaid 格式的 UML 類別圖（含完整說明文件）
2. **uml-class-diagram.puml** - PlantUML 格式的 UML 類別圖

## 如何查看 UML 圖表

### 方法 1: 在 GitHub 上直接查看（推薦）
直接打開 `uml-class-diagram.md` 檔案，GitHub 會自動渲染 Mermaid 圖表。

### 方法 2: 使用 Mermaid Live Editor
1. 訪問 [Mermaid Live Editor](https://mermaid.live/)
2. 複製 `uml-class-diagram.md` 中的 mermaid 程式碼
3. 貼到編輯器中即可看到圖表
4. 可以匯出為 PNG 或 SVG 格式

### 方法 3: 使用 PlantUML
1. 訪問 [PlantUML Online Server](http://www.plantuml.com/plantuml/uml/)
2. 複製 `uml-class-diagram.puml` 的內容
3. 貼到編輯器中生成圖表
4. 可以下載 PNG/SVG 格式

### 方法 4: 使用 VS Code 擴充功能
安裝以下擴充功能之一：
- **Mermaid Preview** (用於 .md 檔案)
- **PlantUML** (用於 .puml 檔案)

## UML 類別圖概覽

本系統包含 **21 個類別**，分為 **6 大模組**：

### 1. 骨架偵測模組 (Skeleton Detection Module)
- `SkeletonDetectionModule` - 主要偵測模組
- `Camera` - 攝影機控制
- `MediapipePose` - Mediapipe 姿態偵測
- `SkeletonKeypoint` - 骨架關鍵點

### 2. 特徵計算模組 (Feature Calculation Module)
- `FeatureCalculationModule` - 特徵計算主模組
- `SkeletonFeature` - 骨架特徵
- `FeatureVariance` - 特徵變異數

### 3. ADHD 預測模組 (ADHD Prediction Module)
- `ADHDPredictionModule` - 預測主模組
- `MLModel` - 機器學習模型
- `PredictionResult` - 預測結果

### 4. 資料輸出模組 (Data Output Module)
- `DataOutputModule` - 輸出控制模組
- `OutputFile` - 檔案管理

### 5. 視覺化模組 (Visualization Module)
- `VisualizationModule` - 視覺化主模組
- `DashboardInterface` - Dash 介面
- `Chart` - 圖表物件
- `DataPoint` - 資料點

### 6. 病歷管理模組 (Medical Record Module)
- `MedicalRecordModule` - 病歷管理主模組
- `RecordDatabase` - 資料庫
- `MedicalRecord` - 病歷記錄
- `Patient` - 病患資訊

## 系統架構特色

1. ✅ **模組化設計** - 6 大獨立模組，易於維護與擴充
2. ✅ **清晰的資料流** - 從影像擷取到結果呈現的完整流程
3. ✅ **完整的類別設計** - 每個類別都有明確的屬性與方法
4. ✅ **符合所有功能需求** - 涵蓋文件中所有功能性與非功能性需求
5. ✅ **本地化儲存** - 確保資料安全性

## 類別關係說明

- **組合關係 (Composition)**: 主系統包含所有子模組
- **關聯關係 (Association)**: 模組之間的使用與依賴關係
- **一對多關係**: 如一個模組產生多個結果物件

## 設計原則

本 UML 設計遵循以下原則：
- **單一職責原則** (Single Responsibility Principle)
- **開放封閉原則** (Open-Closed Principle)
- **依賴反轉原則** (Dependency Inversion Principle)
- **介面隔離原則** (Interface Segregation Principle)

## 相關文件

- [README.md](README.md) - 專案總覽
- [hw3.md](hw3.md) - 功能需求與使用案例
- [plan.md](plan.md) - 專案計劃與工作分配
- [dfd.md](dfd.md) - 系統環境圖 (DFD)
