# AGENTS.md

## 專案概述

這是一個 **Rime 輸入法方案專案**，名為「無蝦」，是蝦米輸入法的簡化版本。

- **專案類型**：Rime 輸入法配置方案
- **方案 ID**：`wuxia`
- **版本**：2026.1.1

## 核心架構

### 配置驅動架構
此專案是純配置型專案，無需編譯或建置步驟。所有功能透過 YAML 配置檔定義。

### 核心檔案結構

#### 1. 方案定義檔

- **wuxia.schema.yaml**：主方案定義檔
  - 定義輸入法的基本資訊（schema_id、name、version、author、description）
  - 配置輸入引擎、處理器、分段器、翻譯器、過濾器
  - 定義按鍵綁定和候選詞顯示規則
  - 方案檔結構包含：
    - `schema`：基本資訊
    - `switches`：功能開關
      - `ascii_mode`：中英文切換
      - `full_shape`：全形/半形切換
      - `extended_charset`：擴充字集切換
      - `simplification`：簡繁轉換
    - `engine`：處理流程定義
      - `processors`：按鍵處理器（ascii_composer、recognizer、key_binder、speller、selector、navigator、express_editor）
      - `segmentors`：輸入分段器（ascii_segmentor、matcher、abc_segmentor、fallback_segmentor）
      - `translators`：翻譯器（table_translator、reverse_lookup_translator）
      - `filters`：過濾器（simplifier、uniquifier）
    - `ascii_composer`：英文模式配置
    - `key_binder`：按鍵綁定設定
      - `Shift + 空格`：切換全形/半形
      - `Ctrl + /`：切換擴充字集
      - `Ctrl + .`：切換簡繁
      - `空格`：清空編碼或上字（依情境而定）
    - `speller`：拼寫規則
      - 定義可用字母表
      - 拼寫代數規則（處理 `~` 前綴）
      - 自動選擇與自動上屏設定
    - `translator`：翻譯器設定
      - 指定字典檔（dictionary: wuxia）
      - 編碼補全、字符集過濾
      - 用戶詞典、句子模式、編碼器相關設定
    - `recognizer`：識別器設定
      - 識別拼音反查模式（`/` 開頭觸發拼音反查）
      - 識別拼音提示模式（`;` 開頭觸發拼音提示）
    - `reverse_lookup`：拼音反查設定
      - 使用 `luna_pinyin` 字典
      - 輸入 `/` 進入拼音反查模式
    - `pinyin_hint`：拼音提示模式設定
      - 輸入 `;` 進入拼音提示模式
      - 使用 `wuxia` 字典，顯示候選字及其拼音註解
    - `pinyin_lookup`：拼音提示過濾器設定
      - 為拼音提示模式的候選字添加拼音註解

#### 2. 字典檔案

- **wuxia.dict.yaml**：主字典檔
  - 作為字典導入的入口點
  - 使用 `import_tables` 機制引入其他字典檔
  - 當前導入：
    - `wuxia.trad`：繁體中文基本字典
    - `wuxia.tradext`：繁體中文擴充詞庫
    - `wuxia.user`：使用者自訂字典
  - 定義字典名稱、版本號、排序方式

- **wuxia.trad.dict.yaml**：繁體中文基本字典
  - 包含繁體中文常用字詞
  - 格式：字詞[Tab]編碼[Tab]權重(可選)
  - 編碼使用無蝦輸入法編碼規則
  - 使用 `~` 前綴標記非標準拆字
  - ⚠️ 使用者不應直接修改此檔案

- **wuxia.tradext.dict.yaml**：繁體中文擴充詞庫
  - 包含擴充字集、罕用字、異體字等
  - 格式與 wuxia.trad.dict.yaml 相同
  - 提供更完整的字詞覆蓋範圍
  - ⚠️ 使用者不應直接修改此檔案

- **wuxia.user.dict.yaml**：使用者自訂字典
  - 專為使用者個人詞彙設計
  - 與官方字典分離，便於備份與遷移
  - 在 import_tables 中排序最後，確保最高優先度
  - 格式與其他字典檔相同
  - ✅ 使用者應在此檔案中新增個人詞彙

#### 3. 全域設定檔

- **default.custom.yaml**：Rime 全域自訂設定
  - 透過 `patch` 機制覆蓋預設設定
  - 目前用於啟用 `wuxia` 方案（在 `schema_list` 中）

### 部署機制

Rime 採用「配置 → 部署 → 生效」的工作流程：
1. 修改 `.yaml` 配置檔
2. 在 Rime 中執行「重新部署」（Deploy）
3. 修改才會生效

### 重新部署指令

**macOS (Squirrel)**:
```bash
/Library/Input\ Methods/Squirrel.app/Contents/MacOS/Squirrel --reload
```

或在命令列執行：
```bash
"/Library/Input Methods/Squirrel.app/Contents/MacOS/Squirrel" --reload
```

## 開發流程

### 測試方式
此專案無傳統單元測試。測試方法為：
1. 修改配置檔案
2. 將修改的檔案複製到 Rime 用戶目錄（macOS: `~/Library/Rime/`）
3. 在 Rime 輸入法中執行「重新部署」
4. 在實際應用中測試輸入法功能
5. 觀察候選詞、按鍵響應、功能開關等是否符合預期

### 修改工作流程

#### 修改方案配置
1. 編輯 `wuxia.schema.yaml` 或 `default.custom.yaml`
2. 確保 YAML 語法正確（縮排使用空格，不使用 Tab）
3. 將修改的檔案複製到 Rime 用戶目錄
4. 透過 Rime 部署功能重新載入配置
5. 實際測試輸入法行為

#### 新增使用者自訂詞彙
1. 編輯 `wuxia.user.dict.yaml`（使用者自訂字典）
2. 在碼表區域（`...` 之後）新增詞條
3. 格式：**字詞[Tab]編碼[Tab]權重(可選)**
   - **重要**：必須使用 **Tab** 鍵分隔，不能使用空格
   - 範例：`範例[Tab]efaa` 或 `測試[Tab]aab[Tab]100`
4. 若要標記非標準拆字，在編碼前加 `~` 前綴
   - 範例：`丶[Tab]~aa`
5. 儲存檔案後，將 `wuxia.user.dict.yaml` 複製到 Rime 用戶目錄
6. 透過 Rime 部署功能重新載入
7. 測試新增的詞條是否正確顯示

**注意**：
- ⚠️ 請勿直接修改 `wuxia.trad.dict.yaml` 或 `wuxia.tradext.dict.yaml`
- 這些是官方字典檔，應保持原樣以便日後更新
- 所有個人詞彙請加入 `wuxia.user.dict.yaml`

#### 修改官方字典（進階使用者）
如果確實需要修改官方字典（如修正錯誤編碼）：
1. 編輯 `wuxia.trad.dict.yaml`（常用字）或 `wuxia.tradext.dict.yaml`（擴充字）
2. 在碼表區域新增或修改詞條
3. 將修改的檔案複製到 Rime 用戶目錄
4. 儲存檔案後重新部署並測試

#### 新增字典檔
1. 建立新的 `.dict.yaml` 檔案
2. 在檔案開頭加入字典資訊：
   ```yaml
   ---
   name: wuxia.新字典名稱
   version: "2026.1.1"
   sort: original
   ...
   ```
3. 在 `wuxia.dict.yaml` 的 `import_tables` 中加入新字典名稱
4. 將新字典檔和修改後的 `wuxia.dict.yaml` 複製到 Rime 用戶目錄
5. 重新部署以載入新字典

### YAML 格式注意事項
- **配置檔（.schema.yaml、.custom.yaml）**：
  - 嚴格使用空格縮排（通常為 2 空格）
  - 不可使用 Tab 字元
  - 保持 YAML 結構的正確性
  - 注意清單項目使用 `-` 符號
  - 字串若包含特殊字元需使用引號
- **字典檔（.dict.yaml）**：
  - YAML 標頭區域（`---` 到 `...` 之間）使用空格縮排
  - 碼表區域**必須**使用 Tab 鍵分隔欄位
  - 每行格式：`字詞[Tab]編碼[Tab]權重(可選)`

## Rime 方案開發要點

### 方案檔結構
典型的完整 Rime 輸入法方案通常還包含：
- **字典檔**（`.dict.yaml`）：定義編碼與字詞的對應關係
- **自訂檔**（`.custom.yaml`）：使用者個人化設定

### 相依性管理
- 若方案依賴其他方案（如拼音反查功能），需在 `schema.dependencies` 中聲明
- 依賴的方案必須已安裝在 Rime 用戶目錄中

### 配置覆蓋機制
- `.custom.yaml` 檔案使用 `patch` 機制
- 可以選擇性覆蓋預設配置，無需完整複製
- 修改後必須重新部署才生效

## 常見任務

由於這是配置型專案，不需要傳統的建置、測試、lint 命令。主要操作為：

### 配置檔案編輯
1. **編輯方案配置**：修改 `wuxia.schema.yaml` 或 `default.custom.yaml`
2. **編輯自訂字典**：修改 `wuxia.user.dict.yaml` 新增個人詞彙
3. **驗證 YAML**：確保語法正確（可使用 YAML 驗證工具）
4. **部署測試**：在 Rime 輸入法中執行重新部署並測試

**注意**：一般使用者不應直接修改 `wuxia.trad.dict.yaml` 或 `wuxia.tradext.dict.yaml`，請使用 `wuxia.user.dict.yaml` 新增個人詞彙。

### 字典管理任務
1. **新增單字或詞彙**：
   - 在 `wuxia.user.dict.yaml` 中新增一行（建議）
   - 格式：`字詞[Tab]編碼`
   - 範例：`範例[Tab]efaa`
   - 重新部署後測試

2. **調整詞頻**：
   - 在詞條後加上權重值（越大越優先）
   - 格式：`字詞[Tab]編碼[Tab]權重`
   - 預設權重為 0

3. **標記非標準拆字**：
   - 在編碼前加 `~` 前綴
   - 格式：`字詞[Tab]~編碼`
   - 這些編碼會在 speller 階段被處理

4. **擴充字典檔**：
   - 建立新的 `.dict.yaml` 檔案
   - 在 `wuxia.dict.yaml` 中新增到 `import_tables`
   - 重新部署以載入新字典

### Rime 用戶目錄位置
- macOS: `~/Library/Rime/`
- Linux: `~/.config/ibus/rime/` 或 `~/.config/fcitx/rime/`
- Windows: `%APPDATA%\Rime\`

方案檔案應放置在 Rime 用戶目錄中才能被識別和使用。
