# FreeCAD MCP

透過 MCP（Model Context Protocol）從 Claude Desktop 控制 FreeCAD。

> Fork 自 [neka-nat/freecad-mcp](https://github.com/neka-nat/freecad-mcp)，加入截圖優化等改進。

## 改進項目

- **截圖回傳檔案路徑**：不再回傳 base64 編碼圖片，避免超過 token 上限
- **自動清理截圖**：只保留最新 5 張，存放於 `~/.freecad-mcp/screenshots/`
- **遠端連線建議使用 SSH Tunnel**：不需要設定 IP 白名單，安全性更高

## 展示

### 設計法蘭

![demo](./assets/freecad_mcp4.gif)

### 設計玩具車

![demo](./assets/make_toycar4.gif)

### 從 2D 圖面建立 3D 模型

#### 輸入 2D 圖面

![input](./assets/b9-1.png)

#### 展示

![demo](./assets/from_2ddrawing.gif)

## 安裝 Addon

FreeCAD Addon 目錄位置：
* Windows：`%APPDATA%\FreeCAD\Mod\`
* Mac：`~/Library/Application\ Support/FreeCAD/Mod/`
* Linux：
  * Ubuntu：`~/.FreeCAD/Mod/` 或 `~/snap/freecad/common/Mod/`（若透過 snap 安裝）
  * Debian：`~/.local/share/FreeCAD/Mod`

將 `addon/FreeCADMCP` 目錄複製到上述 Addon 目錄：

```bash
git clone https://github.com/KCTW/freecad-mcp.git
cd freecad-mcp
cp -r addon/FreeCADMCP ~/.FreeCAD/Mod/
```

安裝完成後需重新啟動 FreeCAD。
在 Workbench 列表中選擇「MCP Addon」即可使用。

![workbench_list](./assets/workbench_list.png)

點擊「FreeCAD MCP」工具列中的「Start RPC Server」啟動 RPC 伺服器。

![start_rpc_server](./assets/start_rpc_server.png)

### 自動啟動 RPC Server

預設需要每次手動啟動 RPC Server。若要自動啟動：

1. 開啟 **FreeCAD MCP** 選單（需先切換到 MCP Addon workbench）
2. 勾選 **Auto-Start Server**

設定會儲存在 `freecad_mcp_settings.json`，下次啟動 FreeCAD 時 RPC Server 會自動啟動。

## 設定 Claude Desktop

需先安裝 [uvx](https://docs.astral.sh/uv/guides/tools/)。

編輯 Claude Desktop 設定檔 `claude_desktop_config.json`：

### 一般使用者

```json
{
  "mcpServers": {
    "freecad": {
      "command": "uvx",
      "args": [
        "freecad-mcp"
      ]
    }
  }
}
```

### 純文字模式（節省 token）

```json
{
  "mcpServers": {
    "freecad": {
      "command": "uvx",
      "args": [
        "freecad-mcp",
        "--only-text-feedback"
      ]
    }
  }
}
```

### 開發者

先 clone 這個 repo：

```bash
git clone https://github.com/KCTW/freecad-mcp.git
```

```json
{
  "mcpServers": {
    "freecad": {
      "command": "uv",
      "args": [
        "--directory",
        "/path/to/freecad-mcp/",
        "run",
        "freecad-mcp"
      ]
    }
  }
}
```

## 遠端連線

### 建議方式：SSH Tunnel（推薦）

最簡單且安全的遠端連線方式，不需要修改任何程式碼或設定 IP：

```bash
ssh -L 9875:localhost:9875 遠端機器
```

兩端都只綁 localhost，SSH 負責加密和認證。

### 其他方式：直接遠端連線

RPC Server 預設只接受本機連線。若需要直接遠端連線：

1. 在 **FreeCAD MCP** 工具列勾選 **Remote Connections**
2. 點擊 **Configure Allowed IPs** 設定允許的 IP，例如：
   ```
   192.168.1.100, 10.0.0.0/24
   ```
3. MCP Server 端加上 `--host` 參數：

```json
{
  "mcpServers": {
    "freecad": {
      "command": "uvx",
      "args": [
        "freecad-mcp",
        "--host", "192.168.1.100"
      ]
    }
  }
}
```

## 工具列表

| 工具 | 說明 |
|------|------|
| `create_document` | 建立新文件 |
| `create_object` | 建立新物件 |
| `edit_object` | 編輯物件 |
| `delete_object` | 刪除物件 |
| `execute_code` | 執行任意 Python 程式碼 |
| `insert_part_from_library` | 從[零件庫](https://github.com/FreeCAD/FreeCAD-library)插入零件 |
| `get_view` | 取得目前視角的截圖（回傳檔案路徑） |
| `get_objects` | 取得文件中所有物件 |
| `get_object` | 取得文件中特定物件 |
| `get_parts_list` | 取得[零件庫](https://github.com/FreeCAD/FreeCAD-library)的零件列表 |

## 截圖機制

本 fork 改進了截圖回傳方式：

- **原版**：截圖以 base64 編碼回傳 → 經常超過 token 上限 → 回應被截斷
- **改進版**：截圖存到 `~/.freecad-mcp/screenshots/` → 回傳檔案路徑 → 輕量快速
- 自動保留最新 5 張截圖，舊的自動刪除

## 授權

MIT License

## 致謝

原始專案：[neka-nat/freecad-mcp](https://github.com/neka-nat/freecad-mcp)
