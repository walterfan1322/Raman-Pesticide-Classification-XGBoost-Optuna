# Raman-Pesticide-Classification

本專案實作了**拉曼光譜**多類別分類流程，結合 **XGBoost** 與 **Optuna** 超參數最佳化，針對多種農藥樣本進行偵測。支援 `.csv` 格式光譜資料，內建交叉驗證、混淆矩陣與多種視覺化工具。

## 專案流程簡介

1. **資料前處理**

   * 數值插值：將波長補齊為整數波長點
   * Min-Max 歸一化：各樣本強度縮放至 0‒1
2. **特徵工程**（可選）

   * PCA 降維或平滑濾波（Savitzky–Golay）
3. **模型訓練**

   * 利用 `xgboost.XGBClassifier` 搭配 Optuna 搜尋最佳超參數
4. **模型評估**

   * **Accuracy**、**F1-score**、**ROC-AUC**
   * 混淆矩陣、特徵重要度圖
5. **模型導出**

   * 儲存為 `joblib` 檔供下游推論使用



## 系統需求

| 軟硬體        | 版本／建議                                          |
| ---------- | ---------------------------------------------- |
| **Python** | 3.9 以上                                         |
| **RAM**    | ≥ 8 GB                                         |
| **GPU**    | 非必要；若啟用 `tree_method="gpu_hist"` 需 NVIDIA CUDA |



## 安裝方式

建議先建立虛擬環境（`conda` 或 `venv` 均可）。

```bash
git clone https://github.com/<yourname>/raman-pesticide-classification.git
cd raman-pesticide-classification
python -m venv .venv
source .venv/bin/activate          # Windows： .venv\Scripts\activate
pip install -r requirements.txt
```



## 檔案結構

| 路徑 / 檔案            | 說明                 |
| ------------------ | ------------------ |
| `experiment.ipynb` | 主程式：資料載入→前處理→訓練→評估 |
| `data/`            | 放置原始光譜 `.csv` 檔案   |
| `output/`          | 儲存模型、報告與圖表         |
| `requirements.txt` | 套件依賴清單             |
| `README.md`        | 本說明文件              |



## 資料格式與命名規則

* **檔案格式**：`<class_label>_<sampleID>.csv`
  **範例**：`Chlorpyrifos_001.csv`
* **CSV 內容**

  ```
  100,101,102,...,1800      ← 第 1 列：波長 (nm)
  0.123,0.117,0.115,...     ← 第 2 列：強度
  ```

> **舉例**：兩份資料 `Chlorpyrifos_001.csv`、`Diazinon_017.csv` 會自動產生 `label = ["Chlorpyrifos", "Diazinon"]` 進行監督式學習。



## 快速開始

```bash
# 啟動 Jupyter Notebook
jupyter notebook experiment.ipynb
```

若欲批次執行，可轉為 Python 腳本：

```bash
jupyter nbconvert --to script experiment.ipynb
python experiment.py
```

## 輸出說明

| 檔案                                 | 內容                      |
| ---------------------------------- | ----------------------- |
| `output/pesticide_raman.joblib`    | 訓練完成之最佳模型               |
| `output/classification_report.txt` | 各類別 Precision、Recall、F1 |
| `output/confusion_matrix.png`      | 測試集混淆矩陣圖                |
| `output/feature_importance.png`    | XGBoost 特徵重要度 Bar plot  |


## 評估指標

* **Accuracy**：整體正確率
* **F1-score**：含 Precision 與 Recall 之調和平均
* **ROC-AUC**：多類別情境下以 One-vs-Rest 計算
* **Confusion Matrix**：檢視各類別易混淆情形


## 常見問題（FAQ）

| 問題             | 解決方式                                            |
| -------------- | ----------------------------------------------- |
| Notebook 找不到資料 | 確認 `data/` 內檔名無中文或空白字元                          |
| Optuna 太慢      | 減少 `n_trials` 或收窄參數範圍                           |
| 無法使用 GPU       | 將 `tree_method` 設為 `"hist"` 或安裝 `xgboost` GPU 版 |


## 參考文獻

* Chen & Guestrin, “XGBoost: A Scalable Tree Boosting System,” *KDD*, 2016.
* Akiba *et al.*, “Optuna: A Next-generation Hyperparameter Optimization Framework,” *KDD*, 2019.
* R. Smith, “Modern Raman Spectroscopy — A Practical Approach,” Wiley, 2019.
