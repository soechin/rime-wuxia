# 無蝦輸入法

[![Rime](https://img.shields.io/badge/RIME-中州韻-green)](https://rime.im/)
[![Version](https://img.shields.io/badge/version-2026.1.1-blue)](https://github.com/soechin/rime-wuxia)

無蝦輸入法是蝦米輸入法的簡化版本，專為 Rime 輸入法框架設計的繁體中文輸入方案。

## 專案特色

- 基於蝦米輸入法編碼系統，簡化設計
- 完整的繁體中文字詞支援
- 擴充字集，包含罕用字與異體字
- 支援簡繁轉換
- 支援全形/半形切換
- 可選擇性顯示擴充字集
- 編碼提示功能，方便學習

## 系統需求

- [Rime 輸入法引擎](https://rime.im/)
  - macOS：[鼠鬚管（Squirrel）](https://github.com/rime/squirrel)
  - Windows：[小狼毫（Weasel）](https://github.com/rime/weasel)
  - Linux：[中州韻（ibus-rime）](https://github.com/rime/ibus-rime)

## 安裝方式

### 方法一：手動安裝

1. 找到您的 Rime 用戶目錄：
   - macOS: `~/Library/Rime/`
   - Windows: `%APPDATA%\Rime\`
   - Linux: `~/.config/ibus/rime/` 或 `~/.config/fcitx/rime/`

2. 將以下檔案複製到 Rime 用戶目錄：
   ```
   wuxia.schema.yaml
   wuxia.dict.yaml
   wuxia.trad.dict.yaml
   wuxia.tradext.dict.yaml
   default.custom.yaml
   ```

3. 重新部署 Rime：
   - macOS：在選單列點擊輸入法圖示 → 重新部署
   - Windows：在系統匣點擊輸入法圖示 → 重新部署
   - Linux：使用 `ibus-daemon -drx` 或 `fcitx-remote -r`

4. 切換到「無蝦」輸入方案即可使用

### 方法二：使用 Git（推薦）

```bash
# 進入 Rime 用戶目錄
cd ~/Library/Rime/  # macOS
# cd %APPDATA%\Rime\  # Windows
# cd ~/.config/ibus/rime/  # Linux

# 複製專案檔案
# 假設您已經下載了此專案的檔案
cp /path/to/rime-wuxia/*.yaml .

# 重新部署
# macOS
"/Library/Input Methods/Squirrel.app/Contents/MacOS/Squirrel" --reload
```

## 使用說明

### 基本輸入

1. 切換到「無蝦」輸入方案
2. 輸入字詞的蝦米編碼
3. 從候選列表中選擇所需字詞
4. 按空格鍵或數字鍵上屏

### 快捷鍵

| 快捷鍵 | 功能 |
|--------|------|
| `Shift + 空格` | 切換全形/半形 |
| `Ctrl + /` | 切換擴充字集（罕用字） |
| `Ctrl + .` | 切換簡繁 |
| `空格` | 清空編碼（輸入中）或選擇首選項（有候選時） |
| `Shift` | 清除當前編碼 |

### 功能開關

- **中英文切換**：預設為中文模式
- **全形/半形**：預設為半形模式
- **擴充字集**：預設不顯示罕用字，可按 `Ctrl + /` 開啟
- **簡繁轉換**：預設為繁體，可按 `Ctrl + .` 切換

## 檔案結構

```
rime-wuxia/
├── wuxia.schema.yaml        # 主方案定義檔
├── wuxia.dict.yaml          # 主字典檔（導入其他字典）
├── wuxia.trad.dict.yaml     # 繁體中文基本字典
├── wuxia.tradext.dict.yaml  # 繁體中文擴充詞庫
├── wuxia.user.dict.yaml     # 使用者自訂字典（您可編輯此檔）
├── default.custom.yaml      # Rime 全域自訂設定
├── AGENTS.md                # 開發者指南
└── README.md                # 本文件
```

## 開發與貢獻

### 新增自訂詞條

如果您想新增個人常用詞彙，請編輯 `wuxia.user.dict.yaml`：

1. 開啟 `wuxia.user.dict.yaml` 檔案
2. 在碼表區域（`...` 之後）加入新詞條
3. 格式：`字詞[Tab]編碼[Tab]權重(可選)`
   - **重要**：必須使用 Tab 鍵分隔，不能用空格
   - 權重為可選，數值越大優先度越高（預設為 0）
4. 儲存後重新部署 Rime
5. 測試新詞條是否正確顯示

範例：
```
範例	efaa
測試	aab	100
```

**注意事項**：
- ⚠️ 請勿直接修改 `wuxia.trad.dict.yaml` 或 `wuxia.tradext.dict.yaml`
- 這些是官方字典檔，應保持原樣以便日後更新
- 所有個人詞彙請加入 `wuxia.user.dict.yaml`

### 備份與遷移

您的自訂詞彙都儲存在 `wuxia.user.dict.yaml` 中，要備份或遷移時：
1. 複製 `wuxia.user.dict.yaml` 到安全位置
2. 在新系統中將此檔案放回 Rime 用戶目錄
3. 重新部署即可恢復所有自訂詞彙

### 修改官方字典（進階使用者）

如果您確實需要修改官方字典：
1. 編輯 `wuxia.trad.dict.yaml`（常用字）或 `wuxia.tradext.dict.yaml`（擴充字）
2. 在碼表區域加入或修改詞條，格式為：`字詞[Tab]編碼[Tab]權重(可選)`
3. 儲存後重新部署 Rime

### 修改方案配置

編輯 `wuxia.schema.yaml` 以調整：
- 按鍵綁定
- 功能開關
- 輸入引擎設定
- 編碼規則

詳細開發指南請參閱 [AGENTS.md](AGENTS.md)。

## 字典說明

### wuxia.trad.dict.yaml
包含繁體中文常用字詞，使用標準無蝦編碼。

### wuxia.tradext.dict.yaml
包含：
- 擴充字集（CJK Extension）
- 罕用字
- 異體字
- 其他特殊字符

### 編碼前綴說明
- 無前綴：標準拆字
- `~` 前綴：非標準拆字（會在 speller 階段被處理）

## 技術規格

- **方案 ID**：wuxia
- **編碼類型**：形碼
- **字符集**：繁體中文（Big5 + Unicode Extension）
- **字典格式**：Rime 標準字典格式
- **排序方式**：original（保持原始順序）

## 常見問題

### Q: 為什麼重新部署後沒有看到「無蝦」方案？
A: 請檢查 `default.custom.yaml` 是否包含以下內容：
```yaml
patch:
  schema_list:
    - schema: wuxia
```

### Q: 如何調整候選詞順序？
A: 在字典檔中，為詞條加上權重值（第三欄），數值越大優先度越高。

### Q: 如何只顯示常用字，不顯示罕用字？
A: 確保「擴充字集」功能處於關閉狀態（預設即為關閉）。

### Q: 可以自訂按鍵綁定嗎？
A: 可以，編輯 `wuxia.schema.yaml` 中的 `key_binder` 區塊。

## 致謝

- [Rime 輸入法引擎](https://rime.im/)
- https://github.com/ianzhuo/rime-liur
- https://github.com/hsuanyi-chou/rime-liur

## 相關連結

- [Rime 官方網站](https://rime.im/)
- [Rime 輸入法說明書](https://github.com/rime/home/wiki)

---

如有問題或建議，歡迎提出 Issue 或 Pull Request。
