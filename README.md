```markdown
# 👔自動化招募系統與人資戰情室

## 🌐 系統核心雙軌模組展示

本系統全面採取「前後端分離」與「雲端大腦」的企業級軟體架構建置，包含以下兩大獨立運作之模組：

1. 👨‍💼 HR 後台招募戰情室 (`app.py`)：內部人資與主管專用。整合 AI 履歷語意解析、主管審核看板、郵件分流狀態機，以及即時招募漏斗（Recruitment Funnel）與技能溢價視覺化儀表板。
2. 📅 應徵者自主面試預約系統 (`streamlit_app.py`)：外部候選人專用。接收 URL 身分參數，自動串接主管 Google Calendar 計算 15 分鐘緩衝期之絕對空檔，供高分候選人線上自主排程並動態生成 Google Meet 會議。

---

## 📋 專案概述與商業價值

在現代人力資源管理（HRIS）與人才數據分析（People Analytics）中，招募漏斗的轉換效率與薪資市場定位是企業組織行為的核心決策依據。

本專案針對傳統招募痛點，開發出端到端的自動化流。透過將「薪資預算」與「核心技術框架」納入 AI 評分權重，並即時動態追蹤求職者最後異動時間（`action_at`），協助企業高層一眼看出招募瓶頸（例如：薪資開太低導致優質即戰力嚴重流失），達成真正數據驅動（Data-Driven）的組織管理決策。

---

## 📁 實際專案結構 (Directory Tree)


hr_automation_project/
├── sample_resumes/             # 📥 測試用履歷：存放供 AI 初篩的應徵者 PDF 範例履歷
├── sample_jd/                  # 📄 測試用職缺說明：供測試 AI 條件萃取與評分邏輯的 JD 檔案
├── credentials.json.example    # 🔑 金鑰範本：供測試者填寫 Google API 憑證之空白格式檔
├── .gitignore                  # 🛡️ 資安防護：指定 Git 忽略憑證與暫存檔之黑名單
├── Calendar_BusyTime_Picker.py # 📡 背景服務：自動向 Google API 請求主管日曆 busy 區間
├── calendar_sync.py            # 🔗 日曆模組：負責建立 Google Calendar 與 Meet 事件
├── app.py                      # 🏢 HR 後台主程式：涵蓋 AI 評分、主管審核、郵件派發與數據儀表板
├── streamlit_app.py            # 📅 應徵者前台主程式：自主預約面試與 Meet 連結生成
├── requirements.txt            # 🗂️ 套件清單：專案執行所需之依賴環境與版本鎖定
├── clear_calendar_events.py    # 📅 刪除面試：刪除行事曆上所有面試活動，方便進行多次的測試（非必要無需執行）
└── DEPLOYMENT_GUIDE.md         # 🚀 部署指南：Google API 憑證申請與環境架設教學

```

> ⚠️ **資安防護規範 (Data Privacy)**：本專案已嚴格配置 `.gitignore` 機制。真實的連線憑證（`credentials.json`、`token.json`）、雲端暫存檔（`calendar_output.json`、`bookings.json`）皆安全隔離於本地伺服器或 Streamlit Secrets 中，切勿推上 GitHub 倉庫。

---

## ☁️ 系統存取與本地執行指南

### 1️⃣ 雲端即時體驗 (Streamlit Cloud)

本系統已全面部署至 Streamlit Cloud，實現免安裝、跨平台的軟體即服務 (SaaS) 體驗。評審與使用者可直接點擊下方連結進行實地操作：

* HR 招募戰情室 (後台)：[點此進入系統](https://ntupbc-hr-dashboard.streamlit.app/)
* 應徵者面試預約 (前台)：[點此進入系統](https://ntupbc-interview-booking.streamlit.app/)

### 2️⃣ 若需於本地端測試執行 (Local Run)

若需在本地環境執行原始碼，請確保已將 Google API 憑證置於專案根目錄，並在終端機輸入以下指令安裝環境：

```bash
pip install -r requirements.txt

```

接著打開兩個獨立的終端機視窗，分別啟動前後台：

```bash
# 視窗 A：啟動「HR 後台招募戰情室」(預設 Port 8501)
streamlit run app.py

# 視窗 B：強制指定 Port 8502 啟動「應徵者預約系統」前台
streamlit run streamlit_app.py --server.port 8502

```

---

## 📊 數據驅動招募演算法邏輯

### 💯 AI 嚴格評分機制（滿分 100 分與思維鏈防呆）

*本系統支援動態規則解析，以下為系統判定邏輯之實務範例：*

* **技術框架一票否決（範例）**：應徵者必須熟練特定框架（如 Flask/Django）。若無相關經驗，AI 將嚴格執行扣分，總分鎖死於 **60 分** 以下，並觸發「AI評分完畢待發感謝信」緩衝狀態。
* **薪資超標預算防呆（範例）**：若 JD 設定月薪上限為 NT$ 70,000，求職者期望薪資一旦超出預算，AI 將啟動思維鏈（Chain of Thought）算術機制，直接在總分**重扣 15 分**。

### 🔄 動態內部追蹤機制 (`action_at`)

為確保 HR 部門之招募服務水準（SLA），系統不單記錄投遞時間（`applied_at`），更在以下三大節點自動連動 `action_at`：

1. **AI 評分完畢** ➔ 寫入初始時間。
2. **主管核准/自動攔截寄信** ➔ 押上最後寄發面邀/感謝信時間。
3. **求職者線上預約完成** ➔ 應徵者點擊確認當下，系統自動將 `action_at` 更新為最新時間，完成無縫查核追蹤。

---

## 🔐 下拉式選單狀態機（Status Machine）

為了防範髒數據（Garbage In, Garbage Out）破壞戰情室分析，Google Sheets 上的 `status` 欄位強烈建議採用「資料驗證」下拉式選單，系統嚴格依循以下狀態機流轉：

```text
[新投遞 PDF] ➔ AI 自動評分 ➔ ⚖️ 分數分流：
               ├── 分數 >= 60 ➔ 【AI評分完畢待主管審核】 ➔ 主管打勾 ➔ TRUE  ➔ 【已發面邀未回覆】➔ 應徵者填寫時間 ➔ 【面試排程已確認】 ➔ HR手動改錄取 ➔ 【已錄取】
               │                                                 └── FALSE ➔ 【已發感謝信】
               └── 分數 < 60  ➔ 【AI評分完畢待發感謝信】 ➔ 靜置滿 48 小時 ➔ 【已發感謝信】

```

*註：若錄取人選放棄或接受其他 Offer，HR 可手動於下拉選單選取 `婉拒offer`。*

---

## 📞 技術支援與常見問題排查

1. **遇到 AI 評分模組回傳 `503 UNAVAILABLE`？**
* 原因：Google Gemini API 伺服器遭遇全球流量尖峰限制。
* 解法：系統已內建自動重試機制。若持續發生，請於 Streamlit Cloud 後台之 `Secrets` 進行 API Key 之熱切換。


2. **想要清空日曆上的大量測試行程？**
* 直接執行 `python clear_calendar_events.py`，輸入 `yes` 即可一鍵秒殺所有含【面試】字樣的測試資料。



---

**最後更新**：2026-06-03

