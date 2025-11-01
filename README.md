# 智慧人流辨識系統

這個專案使用 YOLOv8 模型來進行影片中的人流追蹤與計數。系統能夠自動識別影片中的人物，並使用 ByteTrack 演算法進行追蹤。

## 功能特點

- 🎯 人物偵測與追蹤
- 👥 專注於人流計數（只偵測人類目標）
- 🎬 支援影片檔案處理
- 📊 自動儲存追蹤結果
- 🔍 高精確度的追蹤演算法 (ByteTrack)

## 系統需求

- Python 3.8+
- OpenCV
- Ultralytics YOLOv8
- CUDA 支援（建議用於加速）

## 安裝步驟

1. 安裝必要的 Python 套件：

```bash
pip install ultralytics opencv-python
```

## 使用方法

1. 準備好要分析的影片檔案
2. 執行程式：

```bash
python person_tracking.py
```

3. 依照提示輸入影片檔案路徑
4. 系統會自動處理影片並在 `runs/track/video_analysis/` 資料夾中產生結果

## 參數設定

系統使用以下主要參數：

- `conf`: 0.4（偵測信心度門檻）
- `classes`: [0]（只偵測人類目標）
- `tracker`: "bytetrack.yaml"（使用 ByteTrack 追蹤器）
- `line_thickness`: 1（邊界框線條粗細）

## 輸出說明

系統會在 `runs/track/video_analysis/` 資料夾中產生：
- 標註後的影片檔案
- 追蹤結果資料

## 程式碼範例

```python
import ultralytics
from ultralytics import YOLO
import cv2
import os

def main():
    # 載入影片
    video_path = input("請輸入影片路徑：")
    
    # 載入 YOLOv8 模型
    model = YOLO('yolov8n.pt')
    
    # 執行追蹤
    results = model.track(
        source=video_path,
        tracker="bytetrack.yaml",
        save=True,
        project="runs/track",
        name="video_analysis",
        conf=0.4,
        classes=[0],
        show_labels=False,
        show_conf=False,
        line_thickness=1
    )

if __name__ == "__main__":
    main()
```

## 效能優化建議

1. 使用支援 CUDA 的顯示卡來加速處理
2. 適當調整 `conf` 參數以平衡準確度和效能
3. 若不需要即時顯示，可以關閉 `show` 參數

## 常見問題

1. **Q: 為什麼影片處理速度慢？**
   - A: 檢查是否啟用 GPU 加速
   - A: 可以調低影片解析度來提升處理速度

2. **Q: 如何提高追蹤準確度？**
   - A: 提高 `conf` 值（但可能會降低靈敏度）
   - A: 使用較大的模型（如 yolov8m.pt 或 yolov8l.pt）

## 參考資源

- [YOLOv8 官方文件](https://docs.ultralytics.com/)
- [ByteTrack 論文](https://arxiv.org/abs/2110.06864)
- [OpenCV 文件](https://docs.opencv.org/)

## 未來可以加入的功能（建議）

以下為建議可作為後續開發優先項目的功能清單，按照功能類型分組並附上簡短說明：

- 人數統計功能
    - 即時計算畫面中的人數（依 track_id 估算目前在線人數）
    - 產生每日/每小時的人流統計報表（CSV/圖表）
    - 設定人數上限並在超載時發出警示（log / webhook / 電子郵件）

- 軌跡分析
    - 記錄與視覺化個體移動路徑（輸出軌跡 CSV）
    - 產生熱力圖以顯示人流密集區域（PNG 或 覆蓋影片）
    - 分析停留時間與常見行為模式（停留排行、平均停留時長）

- 進階影像處理
    - 人臉 / 車牌馬賽克以保護隱私（輸出原始/保護兩種版本）
    - 支援夜間 / 紅外攝影機影像的前處理（簡易增亮 / 對比調整）
    - 影像穩定化 / 防震處理以改善偵測穩定性

- 多攝影機整合
    - 支援多個攝影機同時運作（file / RTSP / webcam）
    - 跨鏡頭人物關聯（re-identification baseline 或時間/區域啟發式關聯）
    - 立體空間定位（進階，可於多鏡頭場域作位置估算）

- 智慧警示系統
    - 偵測異常群聚行為並觸發警示
    - 特定區域人數超載即時告警
    - 支援即時推送通知（Webhook / Email / 第三方服務）

- 資料分析與報表
    - 自動產生日/週/月報表與圖表（PNG / CSV）
    - 時序分析與簡單預測（移動平均、ARIMA 範例）

- 系統整合
    - 與門禁系統整合，或將結果推送至電子看板
    - 提供 REST API 供其他系統查詢即時人數與歷史統計

- UI / UX 與遠端監控
    - 開發網頁監控介面（Streamlit / Flask / FastAPI + 前端）
    - 手機 APP 或手機版儀表板（簡化視圖）

- AI 增強功能（選項性）
    - 年齡層與性別推估、情緒辨識（以提升分析維度）
    - 基於軌跡與停留行為的異常行為預測模型

- 效能優化與邊緣運算
    - GPU 加速（CUDA / Torch device 偵測）與 batch / 非同步處理
    - 影像壓縮與輕量化選項以支援邊緣裝置（Raspberry Pi / NVIDIA Jetson）

您可以告訴我希望我把上面哪幾項轉成 README 中的核取清單（checkbox）或要我直接把其中某幾項實作成範例（例如：即時人數統計 / 結果匯出 CSV / Heatmap），我會依您選擇的項目繼續實作與驗證。