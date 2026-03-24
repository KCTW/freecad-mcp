# 快速入門指南

5 分鐘內開始使用 FreeCAD MCP！

## 前置需求

- Python 3.10 或更高版本
- FreeCAD 0.21 或更高版本已安裝
- Claude Desktop 已安裝

## 步驟 1：安裝 FreeCAD 附加元件（2 分鐘）

### 找到您的 FreeCAD 附加元件目錄

**Windows：**
```
%APPDATA%\FreeCAD\Mod\
```
通常位於：`C:\Users\YourName\AppData\Roaming\FreeCAD\Mod\`

**Mac：**
```
~/Library/Application Support/FreeCAD/Mod/
```

**Linux：**
- Ubuntu：`~/.FreeCAD/Mod/`
- Ubuntu (snap)：`~/snap/freecad/common/Mod/`
- Debian：`~/.local/share/FreeCAD/Mod`

### 安裝附加元件

```bash
# 複製儲存庫
git clone https://github.com/KCTW/freecad-mcp.git
cd freecad-mcp

# 複製附加元件（根據您的作業系統調整路徑）
# Windows
xcopy /E /I addon\FreeCADMCP "%APPDATA%\FreeCAD\Mod\FreeCADMCP"

# Mac/Linux
cp -r addon/FreeCADMCP ~/.FreeCAD/Mod/
```

## 步驟 2：啟動 FreeCAD 伺服器（30 秒）

1. **啟動 FreeCAD**

2. **選擇 MCP Addon 工作台**
   - 在工作台下拉選單中找到「MCP Addon」

3. **啟動 RPC 伺服器**
   - 點擊工具列中的「Start RPC Server」按鈕
   - 伺服器將在連接埠 9875 上啟動
   - 您應該會看到確認訊息

![Start Server](../assets/start_rpc_server.png)

## 步驟 3：設定 Claude Desktop（1 分鐘）

### 找到設定檔

**Windows：**
```
%APPDATA%\Claude\claude_desktop_config.json
```

**Mac：**
```
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Linux：**
```
~/.config/Claude/claude_desktop_config.json
```

### 新增設定

開啟檔案並新增：

```json
{
  "mcpServers": {
    "freecad": {
      "command": "uvx",
      "args": ["freecad-mcp"]
    }
  }
}
```

**選用：純文字模式**（節省 token，不含截圖）：
```json
{
  "mcpServers": {
    "freecad": {
      "command": "uvx",
      "args": ["freecad-mcp", "--only-text-feedback"]
    }
  }
}
```

### 重新啟動 Claude Desktop

關閉並重新開啟 Claude Desktop 以使變更生效。

## 步驟 4：測試一下！（1 分鐘）

開啟 Claude Desktop 並嘗試以下指令：

### 測試 1：簡單方塊
```
Create a box 100mm x 50mm x 30mm
```

**預期結果：** 在 FreeCAD 中建立方塊並顯示截圖

### 測試 2：進階形狀
```
Create a cylinder with radius 20mm and height 50mm,
then add a fillet of 5mm to the top edge
```

**預期結果：** 帶有圓角頂邊的圓柱體

### 測試 3：對稱零件
```
Create a cube 50mm on each side, then mirror it across the YZ plane
```

**預期結果：** 兩個並排的立方體

## 成功！

您現在已準備好使用 FreeCAD 搭配 Claude！

## 下一步

### 深入學習

- **[使用者指南](USER_GUIDE.md)** - 完整功能文件
- **[Corsair 工作流程](CORSAIR_MODELING_WORKFLOW.md)** - 進階飛機建模
- **[API 參考](API_REFERENCE.md)** - 所有可用工具

### 嘗試這些範例

#### 範例 1：設計法蘭
```
Create a flange for a pipe:
1. Create a cylinder 100mm diameter, 20mm thick
2. Add 6 bolt holes in a circular pattern at 80mm diameter
3. Each hole is 10mm diameter
```

#### 範例 2：飛機機翼剖面
```
Create an aircraft wing:
1. Import NACA 2412 airfoil profile with 2000mm chord length
2. Extrude it 6000mm to create a wing
3. Mirror it to create both wings
```

#### 範例 3：星型引擎
```
Create a radial engine cylinder arrangement:
1. Create one cylinder 50mm diameter, 100mm long
2. Use circular pattern to create 18 cylinders around an axis
```

## 疑難排解

### 問題：Claude 看不到 FreeCAD 工具

**解決方法：**
1. 檢查 FreeCAD RPC 伺服器是否正在執行（綠色指示燈）
2. 重新啟動 Claude Desktop
3. 確認設定檔語法正確

### 問題：「Connection refused」

**解決方法：**
1. 確認 FreeCAD 正在執行
2. 在 FreeCAD 中點擊「Start RPC Server」
3. 檢查防火牆是否允許 localhost:9875

### 問題：回應中沒有截圖

**解決方法：**
1. 在 FreeCAD 中切換到 3D 視圖（不是 TechDraw/Spreadsheet）
2. 或者如果不需要截圖，使用 `--only-text-feedback` 參數

### 問題：工具無法執行

**解決方法：**
1. 檢查 FreeCAD 主控台的錯誤訊息
2. 嘗試在 FreeCAD Python 主控台中手動執行程式碼
3. 確認 FreeCAD 版本為 0.21 或更高

## 取得協助

- **GitHub Issues**：[回報錯誤或請求功能](https://github.com/KCTW/freecad-mcp/issues)
- **文件**：查看[使用者指南](USER_GUIDE.md)
- **FreeCAD 論壇**：[詢問 FreeCAD 相關問題](https://forum.freecad.org/)

## 秘訣與技巧

### 描述要具體
與其說：「Make a part」
不如說：「Create a rectangular part 100mm x 50mm x 20mm」

### 使用逐步描述
對於複雜零件，逐一描述操作：
```
1. Create a cylinder 50mm diameter, 100mm height
2. Add a 10mm fillet to the top edge
3. Mirror it across the XZ plane
4. Move the copy 100mm in the Y direction
```

### 引用現有物件
```
"Add a hole to the top face of Box001"
"Mirror Cylinder001 across the YZ plane"
```

### 儲存您的工作
FreeCAD 不會自動儲存。在 FreeCAD 中：
```
File > Save As > choose location
```

### 視覺化結果
要求特定視圖：
```
"Show me the top view"
"Show me an isometric view"
```

## 接下來呢？

現在您已經讓 FreeCAD MCP 運作起來了，可以探索：

1. **[完整使用者指南](USER_GUIDE.md)** - 所有 52 個工具的說明
2. **[Corsair 工作流程](CORSAIR_MODELING_WORKFLOW.md)** - 實際飛機專案
3. **[貢獻指南](../CONTRIBUTING.md)** - 協助改善 FreeCAD MCP

---

**準備好設計了嗎？開始使用 Claude 和 FreeCAD 創作吧！**
