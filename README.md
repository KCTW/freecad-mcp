# FreeCAD MCP

透過 MCP（Model Context Protocol）從 Claude Desktop 控制 FreeCAD。

> Fork 自 [neka-nat/freecad-mcp](https://github.com/neka-nat/freecad-mcp)，加入截圖優化與 v3.0 進階功能。

## 安裝

```bash
git clone https://github.com/KCTW/freecad-mcp.git
cd freecad-mcp
cp -r addon/FreeCADMCP ~/.FreeCAD/Mod/   # 複製到 FreeCAD Addon 目錄
```

重啟 FreeCAD → 選擇「MCP Addon」→ 點擊「Start RPC Server」

## 設定 Claude Desktop

編輯 `claude_desktop_config.json`：

```json
{
  "mcpServers": {
    "freecad": {
      "command": "uv",
      "args": ["--directory", "/path/to/freecad-mcp/", "run", "freecad-mcp"]
    }
  }
}
```

## 遠端連線

建議用 SSH Tunnel，不需要設定 IP：

```bash
ssh -L 9875:localhost:9875 遠端機器
```

## 與原版的差異

- 截圖回傳**檔案路徑**而非 base64，避免超過 token 上限
- 自動清理截圖，只保留最新 5 張
- 整合 v3.0 進階功能（Sketch、布林運算、Assembly）

## 文件

- [Quick Start](docs/QUICKSTART.md) · [User Guide](docs/USER_GUIDE.md) · [API Reference](docs/API_REFERENCE.md)

## 授權

MIT License · 原始專案：[neka-nat/freecad-mcp](https://github.com/neka-nat/freecad-mcp)
