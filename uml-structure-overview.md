# UML 類別圖結構總覽

## 智慧型兒童ADHD風險評估系統 - 類別階層架構

```
┌─────────────────────────────────────────────────────────────────┐
│                    ADHDAssessmentSystem                         │
│                      (主系統控制器)                              │
│  - systemName: String                                           │
│  - version: String                                              │
│  - currentDate: Date                                            │
│  + initialize(), shutdown(), getSystemStatus()                  │
└────────────┬────────────────────────────────────────────────────┘
             │
             ├─────────────────────────────────────────┐
             │                                         │
    ┌────────▼─────────┐                    ┌─────────▼──────────┐
    │ 骨架偵測模組      │                    │ 特徵計算模組        │
    └──────────────────┘                    └────────────────────┘
    │                                       │
    ├─ SkeletonDetectionModule             ├─ FeatureCalculationModule
    │  ├─ Camera                            │  ├─ SkeletonFeature
    │  ├─ MediapipePose                     │  └─ FeatureVariance
    │  └─ SkeletonKeypoint                  │
    │                                       │
    │                                       │
    ├─────────────────────┬─────────────────┴──────────┐
    │                     │                            │
┌───▼──────────┐  ┌──────▼─────────┐      ┌──────────▼─────────┐
│ ADHD預測模組  │  │ 資料輸出模組    │      │ 視覺化模組          │
└──────────────┘  └────────────────┘      └────────────────────┘
│                 │                       │
├─ ADHDPredictionModule                   ├─ VisualizationModule
│  ├─ MLModel    ├─ DataOutputModule      │  ├─ DashboardInterface
│  └─ PredictionResult                    │  ├─ Chart
│                 └─ OutputFile           │  └─ DataPoint
│                                         │
│                                         │
└─────────────────┬───────────────────────┘
                  │
         ┌────────▼─────────┐
         │ 病歷管理模組      │
         └──────────────────┘
         │
         ├─ MedicalRecordModule
         │  ├─ RecordDatabase
         │  ├─ MedicalRecord
         │  └─ Patient
```

## 詳細類別列表

### 🔵 模組 1: 骨架偵測模組 (Skeleton Detection Module)

#### 1.1 SkeletonDetectionModule
```
屬性:
  - camera: Camera
  - poseDetector: MediapipePose
  - isRunning: boolean
  - countdownTimer: int

方法:
  + startDetection(): void
  + stopDetection(): void
  + captureFrame(): Frame
  + getSkeletonData(): List<SkeletonKeypoint>
```

#### 1.2 Camera
```
屬性:
  - cameraID: int
  - resolution: int
  - frameRate: int

方法:
  + open(): void
  + close(): void
  + captureImage(): Image
  + getVideoStream(): VideoStream
```

#### 1.3 MediapipePose
```
屬性:
  - modelPath: String
  - confidenceThreshold: float

方法:
  + detectPose(image): Pose
  + extractKeypoints(image): List<Keypoint>
  + getLandmarks(): List<Landmark>
```

#### 1.4 SkeletonKeypoint
```
屬性:
  - keypointID: int
  - keypointName: String
  - x, y, z: float
  - visibility: float

方法:
  + getCoordinates(): Coordinate
  + getVisibility(): float
```

---

### 🟢 模組 2: 特徵計算模組 (Feature Calculation Module)

#### 2.1 FeatureCalculationModule
```
屬性:
  - windowSize: int
  - slideStep: int
  - features: List<SkeletonFeature>

方法:
  + calculateFeatures(skeletonData): List<SkeletonFeature>
  + calculateVariance(features): List<FeatureVariance>
  + getSlidingWindowData(): List
```

#### 2.2 SkeletonFeature
```
屬性:
  - featureName: String
  - featureValue: float
  - featureType: String

方法:
  + calculateAngle(p1, p2, p3): float
  + calculateDistance(p1, p2): float
  + calculateRatio(l1, l2): float
```

#### 2.3 FeatureVariance
```
屬性:
  - featureName: String
  - variance: float
  - mean: float
  - stdDev: float
  - windowIndex: int

方法:
  + computeVariance(values): float
  + getStatistics(): Statistics
```

---

### 🟡 模組 3: ADHD 預測模組 (ADHD Prediction Module)

#### 3.1 ADHDPredictionModule
```
屬性:
  - model: MLModel
  - modelPath: String
  - threshold: float

方法:
  + loadModel(): void
  + predict(features): PredictionResult
  + getProbability(): float
  + saveResults(): void
```

#### 3.2 MLModel
```
屬性:
  - modelType: String
  - modelVersion: String
  - trainedDate: Date

方法:
  + load(modelPath): void
  + predict(inputData): float
  + getConfidence(): float
```

#### 3.3 PredictionResult
```
屬性:
  - adhdProbability: float
  - timestamp: Date
  - patientID: String
  - analysisID: String

方法:
  + getProbability(): float
  + getTimestamp(): Date
  + exportToCSV(): void
```

---

### 🔴 模組 4: 資料輸出模組 (Data Output Module)

#### 4.1 DataOutputModule
```
屬性:
  - outputDirectory: String
  - fileFormat: String

方法:
  + saveSkeletonSequence(data, filename): void
  + saveFeatureVariance(data, filename): void
  + savePredictionResult(result, filename): void
  + saveVideo(videoData, filename): void
  + exportCSV(data, filename): void
```

#### 4.2 OutputFile
```
屬性:
  - fileName: String
  - filePath: String
  - fileType: String
  - createdDate: Date
  - fileSize: long

方法:
  + create(): void
  + write(data): void
  + close(): void
  + getPath(): String
```

---

### 🟣 模組 5: 視覺化模組 (Visualization Module)

#### 5.1 VisualizationModule
```
屬性:
  - dashboard: DashboardInterface
  - charts: List<Chart>

方法:
  + loadLatestResults(): void
  + displayProbabilityChart(): void
  + displayVarianceChart(): void
  + updateRealtime(): void
```

#### 5.2 DashboardInterface
```
屬性:
  - interfaceTitle: String
  - refreshRate: int
  - autoUpdate: boolean

方法:
  + initialize(): void
  + refresh(): void
  + loadData(resultData): void
  + displayCharts(): void
```

#### 5.3 Chart
```
屬性:
  - chartType: String
  - title: String
  - dataPoints: List<DataPoint>

方法:
  + createLineChart(): Chart
  + createBarChart(): Chart
  + updateData(newData): void
  + render(): void
```

#### 5.4 DataPoint
```
屬性:
  - xValue: float
  - yValue: float
  - label: String
  - timestamp: Date

方法:
  + getCoordinates(): Coordinate
  + getLabel(): String
```

---

### 🟠 模組 6: 病歷管理模組 (Medical Record Module)

#### 6.1 MedicalRecordModule
```
屬性:
  - database: RecordDatabase
  - storageLocation: String

方法:
  + createRecord(patientID): MedicalRecord
  + searchRecord(keyword): List<MedicalRecord>
  + updateRecord(recordID, data): void
  + deleteRecord(recordID): void
  + filterByDate(startDate, endDate): List
  + filterByKeyword(keyword): List
```

#### 6.2 RecordDatabase
```
屬性:
  - databasePath: String
  - records: List<MedicalRecord>

方法:
  + connect(): void
  + disconnect(): void
  + query(criteria): List
  + insert(record): void
  + update(record): void
  + delete(recordID): void
```

#### 6.3 MedicalRecord
```
屬性:
  - recordID: String
  - patientID: String
  - patientName: String
  - age: int
  - gender: String
  - recordDate: Date
  - analysisResults: List<PredictionResult>
  - videoFiles: List<String>

方法:
  + getRecordInfo(): RecordInfo
  + addAnalysisResult(result): void
  + updatePatientInfo(info): void
  + exportRecord(): void
```

#### 6.4 Patient
```
屬性:
  - patientID: String
  - name: String
  - age: int
  - gender: String
  - birthDate: Date
  - contactInfo: String

方法:
  + getPatientInfo(): PatientInfo
  + updateInfo(newInfo): void
```

---

## 類別關係圖示說明

### 關係類型
- `*--` : 組合 (Composition) - 強擁有關係
- `o--` : 聚合 (Aggregation) - 弱擁有關係
- `-->` : 關聯 (Association) - 使用關係
- `--|>` : 繼承 (Inheritance) - 父子類關係

### 多重性標記
- `1` : 一個
- `*` : 零個或多個
- `0..1` : 零個或一個
- `1..*` : 一個或多個

---

## 資料流向

```
影像擷取 (Camera)
    ↓
骨架偵測 (MediapipePose)
    ↓
關鍵點萃取 (SkeletonKeypoint)
    ↓
特徵計算 (SkeletonFeature)
    ↓
變異數計算 (FeatureVariance)
    ↓
ADHD 預測 (MLModel)
    ↓
結果輸出 (PredictionResult)
    ↓
┌───────────┴───────────┐
│                       │
視覺化呈現              資料儲存
(Dashboard)          (OutputFile)
                         │
                    病歷管理
                (MedicalRecord)
```

---

## 統計資訊

- **總類別數**: 21
- **總模組數**: 6
- **關係數量**: 27
- **屬性總數**: 約 80+
- **方法總數**: 約 70+

## 設計模式應用

1. **單例模式 (Singleton)**: ADHDAssessmentSystem
2. **工廠模式 (Factory)**: OutputFile 建立
3. **策略模式 (Strategy)**: MLModel 預測演算法
4. **觀察者模式 (Observer)**: DashboardInterface 即時更新
5. **資料訪問物件 (DAO)**: RecordDatabase
