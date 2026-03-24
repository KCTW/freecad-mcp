# FreeCAD MCP 使用指南

使用 Claude Desktop 操作 FreeCAD MCP 的完整指南。

## 目錄

1. [簡介](#簡介)
2. [開始使用](#開始使用)
3. [基本工作流程](#基本工作流程)
4. [進階功能](#進階功能)
5. [組裝工作流程](#組裝工作流程)
6. [最佳實踐](#最佳實踐)
7. [疑難排解](#疑難排解)

---

## 簡介

FreeCAD MCP 讓您能夠透過 Claude Desktop 以自然語言控制 FreeCAD。無需學習複雜的 CAD 介面，只需描述您想建立的內容，Claude 就會產生對應的 FreeCAD 指令。

### 功能概覽

- 📦 建立參數化 3D 物件
- ✏️ 使用草圖與擠出進行設計
- 🔧 套用布林運算（聯集、差集、交集）
- 🎨 加入細節修飾（圓角、倒角、薄殼）
- 🔄 變換與陣列複製物件
- 🏗️ 使用約束建立組裝件
- ✈️ 設計複雜專案，如飛機

### 系統架構

```
Claude Desktop (使用者介面)
    ↓ 自然語言
Model Context Protocol (MCP)
    ↓ Tool Calls
FreeCAD MCP Server
    ↓ XML-RPC
FreeCAD (CAD 引擎)
```

---

## 開始使用

### 先決條件

開始之前，請確認您已安裝：
- ✅ FreeCAD 0.21+ 已安裝
- ✅ Python 3.10+ 已安裝
- ✅ Claude Desktop 已安裝
- ✅ FreeCAD MCP 附加元件已安裝
- ✅ Claude Desktop 已完成設定

安裝說明請參閱[快速入門指南](QUICKSTART.md)。

### 第一步

1. **啟動 FreeCAD**
   - 開啟 FreeCAD
   - 選擇「MCP Addon」工作台
   - 點擊「Start RPC Server」

2. **開啟 Claude Desktop**
   - 您應該會在可用工具中看到「freecad」
   - 若未顯示，請重新啟動 Claude Desktop

3. **嘗試簡單指令**
   ```
   "Create a box 100mm x 50mm x 30mm"
   ```

4. **檢查 FreeCAD**
   - 您應該會看到已建立的方塊
   - 截圖應會顯示在 Claude 中

---

## 基本工作流程

### 建立簡單形狀

#### 方塊
```
"Create a rectangular box 100mm long, 50mm wide, 30mm high"
```

#### 圓柱
```
"Create a cylinder with radius 20mm and height 50mm"
```

#### 球體
```
"Create a sphere with radius 30mm"
```

### 定位物件

#### 絕對位置
```
"Move Box001 to position x=100, y=50, z=0"
```

#### 相對移動
```
"Move Cylinder001 10mm in the positive X direction"
```

### 修改物件

#### 變更尺寸
```
"Change the height of Cylinder001 to 75mm"
```

#### 變更顏色
```
"Make Box001 red"
```

### 刪除物件

```
"Delete Cylinder001"
```

---

## 進階功能

### 基於草圖的建模

這是建立複雜零件的專業工作流程。

#### 步驟一：建立基準平面

```
"Create a datum plane aligned with XY at the origin"
```

**結果：** 建立 `base_plane`（含有原點的 Body）

#### 步驟二：建立草圖

```
"Create a sketch on base_plane"
```

**結果：** 建立 `base_plane_sketch`

#### 步驟三：加入幾何圖形

```
"Add a rectangle 100mm x 50mm to the sketch, starting at origin"
```

**詳細資料：**
```json
{
  "geometry_elements": [
    {"type": "line", "start": {"x": 0, "y": 0}, "end": {"x": 100, "y": 0}},
    {"type": "line", "start": {"x": 100, "y": 0}, "end": {"x": 100, "y": 50}},
    {"type": "line", "start": {"x": 100, "y": 50}, "end": {"x": 0, "y": 50}},
    {"type": "line", "start": {"x": 0, "y": 50}, "end": {"x": 0, "y": 0}}
  ]
}
```

#### 步驟四：擠出

```
"Extrude base_plane_sketch 20mm"
```

**結果：** 建立 `base_plane_solid`

### 布林運算

#### 聯集（融合）

將多個實體合併為一個。

```
"Fuse Box001 and Cylinder001 together"
```

**使用情境：** 將零件合併為單一實體

#### 差集（減去）

從一個實體中移除另一個實體。

```
"Cut Cylinder001 from Box001 to create a hole"
```

**使用情境：** 建立孔洞、凹槽、切口

#### 交集

僅保留重疊的部分。

```
"Find the intersection between Sphere001 and Box001"
```

**使用情境：** 建立複雜形狀

### 修飾操作

#### 圓角（圓滑邊緣）

```
"Add a 5mm fillet to edges 1, 2, and 5 of Box001"
```

**使用情境：** 專業外觀、減少應力集中

#### 倒角（斜切邊緣）

```
"Add a 2mm chamfer to edge 3 of Box001"
```

**使用情境：** 去除銳邊、組裝間隙

#### 薄殼（挖空）

```
"Create a hollow shell from Box001 with 2mm wall thickness, removing the top face"
```

**使用情境：** 外殼、容器、輕量化零件

### 變換與陣列

#### 鏡像

即時建立對稱零件。

```
"Mirror Wing across the YZ plane and merge with original"
```

**生產力：** 對稱設計可節省 60% 的時間

#### 環形陣列

建立徑向排列。

```
"Create a circular pattern of Cylinder001, 18 copies around the Z axis"
```

**使用情境：** 星型引擎、螺栓排列、齒輪

#### 線性陣列

建立矩形排列。

```
"Create a linear pattern of Hole001, 5 copies spaced 50mm apart in X direction"
```

**使用情境：** 重複特徵、螺栓孔、通風口

### 進階建模

#### 放樣

在多個輪廓之間建立有機形狀。

```
"Create a loft between Profile1, Profile2, and Profile3"
```

**使用情境：** 飛機機身、船體、瓶子

#### 旋轉

建立旋轉體零件。

```
"Revolve Profile001 360 degrees around the Z axis"
```

**使用情境：** 瓶子、軸、滑輪、碗

#### 掃掠

沿路徑掃掠輪廓。

```
"Sweep Circle001 along Spline001"
```

**使用情境：** 管道、纜線、扶手

### 參考幾何

#### 自訂平面

```
"Create a plane at Z=100 with normal tilted 10 degrees"
```

**使用情境：** 斜角特徵、複雜組裝

#### NACA 翼型輪廓

```
"Import NACA 2412 airfoil with 2000mm chord length"
```

**使用情境：** 飛機機翼、渦輪葉片、船龍骨

#### DXF 匯入

```
"Import the DXF file from C:/drawings/part.dxf as a sketch"
```

**使用情境：** 專業 CAD 工作流程整合

---

## 組裝工作流程

### Assembly3（基於約束）

根據約束自動定位。

#### 步驟一：建立組裝

```
"Create an Assembly3 container named MainAssembly"
```

#### 步驟二：加入零件

```
"Add Box001 to MainAssembly"
"Add Cylinder001 to MainAssembly"
```

#### 步驟三：加入約束

```
"Add a PlaneCoincident constraint between Face6 of Box001 and Face1 of Cylinder001"
```

#### 步驟四：求解

```
"Solve the assembly constraints"
```

**結果：** 零件自動定位至正確位置

### Assembly4（基於 LCS）

使用座標系統進行手動定位。

#### 步驟一：建立組裝

```
"Create an Assembly4 container named MainAssembly"
```

#### 步驟二：建立 LCS

```
"Create an LCS named LCS_Base at origin in MainAssembly"
```

#### 步驟三：插入零件

```
"Insert part from C:/parts/base.FCStd into MainAssembly, attach to LCS_Base"
```

### 材料清單

```
"Generate a BOM for MainAssembly in markdown format"
```

**結果：** 包含零件數量、質量、材料的表格

---

## 最佳實踐

### 1. 規劃

**開始之前：**
- 在紙上畫出設計草圖
- 將設計分解為元件
- 找出對稱特徵
- 規劃組裝結構

### 2. 命名慣例

**使用描述性名稱：**
- ✅ `fuselage_main_body`
- ✅ `wing_left_outer`
- ❌ `Pad001`
- ❌ `Cut042`

### 3. 漸進式複雜度

**逐步建立：**
1. 從簡單形狀開始
2. 測試基本操作
3. 逐步加入細節
4. 經常儲存

### 4. 善用對稱

**對稱零件使用鏡像：**
```
"Create the right wing, then mirror it to create both wings"
```
**可節省 50% 以上的時間**

### 5. 使用陣列

**重複特徵使用陣列：**
```
"Create one bolt hole, then use circular pattern for 6 holes"
```
**比逐一建立孔洞快得多**

### 6. 隨時記錄

**在 Claude 中描述您的步驟：**
```
"Create fuselage base (will be 4000mm long, elliptical cross-section)"
```

### 7. 增量儲存

**在 FreeCAD 中：**
- 檔案 > 另存新檔 > `project_v1.FCStd`
- 進行修改
- 檔案 > 另存新檔 > `project_v2.FCStd`

### 8. 驗證尺寸

**使用截圖檢查：**
```
"Show me the top view"
"Show me the dimensions of Box001"
```

### 9. 組織複雜專案

**使用層級結構：**
```
Assembly
├── Fuselage
│   ├── Forward section
│   ├── Center section
│   └── Tail section
├── Wings
│   ├── Left wing
│   └── Right wing
└── Empennage
```

### 10. 進行複雜操作前先測試

**先在簡單幾何上嘗試操作：**
```
"Create a test box and try the fillet operation before applying to main part"
```

---

## 疑難排解

### 常見問題

#### 問題：Claude 看不到 FreeCAD 工具

**症狀：**
- Claude 顯示「I don't have access to FreeCAD」
- 沒有可用工具

**解決方案：**
1. 重新啟動 Claude Desktop
2. 檢查 `claude_desktop_config.json` 語法
3. 確認 FreeCAD RPC 伺服器正在執行
4. 檢查 Claude Desktop 日誌

#### 問題：連線被拒絕

**症狀：**
- 錯誤：「Connection refused to localhost:9875」
- 工具無法執行

**解決方案：**
1. 啟動 FreeCAD
2. 選擇「MCP Addon」工作台
3. 點擊「Start RPC Server」
4. 確認綠色指示燈亮起
5. 檢查防火牆設定

#### 問題：沒有截圖

**症狀：**
- 操作成功但無圖片
- 顯示「Screenshot unavailable」訊息

**解決方案：**
1. 切換至 3D 視圖（非 TechDraw/Spreadsheet）
2. 若不需要截圖，使用 `--only-text-feedback`
3. 確認 FreeCAD 活動文件具有 3D 視圖

#### 問題：找不到物件

**症狀：**
- 錯誤：「Object 'Box001' not found」

**解決方案：**
1. 列出物件：`"Show me all objects in the document"`
2. 確認物件名稱拼寫正確
3. 檢查正確的文件是否為活動狀態
4. 使用 `get_objects` 重新整理

#### 問題：布林運算失敗

**症狀：**
- 聯集/差集/交集時發生錯誤
- 結果無效

**解決方案：**
1. 確認物件是有效的實體
2. 檢查物件是否確實重疊
3. 嘗試使用 `execute_code` 檢查幾何
4. 若幾何複雜，嘗試簡化

#### 問題：組裝約束無法求解

**症狀：**
- 已加入約束但零件不移動
- 求解器錯誤

**解決方案：**
1. 檢查約束類型是否適當
2. 確認參考（面/邊）存在
3. 檢查是否有衝突的約束
4. 嘗試改用 Assembly4 的 LCS 方式

### 取得幫助

1. **查閱文件**
   - [API 參考](API_REFERENCE.md)
   - [快速入門](QUICKSTART.md)
   - [Corsair 工作流程](CORSAIR_MODELING_WORKFLOW.md)

2. **FreeCAD 主控台**
   - 檢視 > 面板 > Python 主控台
   - 檢查錯誤訊息

3. **GitHub Issues**
   - [回報錯誤](https://github.com/KCTW/freecad-mcp/issues)
   - 搜尋現有問題

4. **FreeCAD 論壇**
   - [FreeCAD 論壇](https://forum.freecad.org/)
   - 詢問 FreeCAD 相關問題

---

## 範例專案

### 專案一：簡單法蘭

```
1. "Create a cylinder 100mm diameter, 20mm thick"
2. "Add 6 holes, 10mm diameter, in a circular pattern at 80mm diameter"
3. "Add 5mm fillet to outer edges"
```

**時間：** 約 2 分鐘

### 專案二：飛機機翼

```
1. "Import NACA 2412 airfoil profile with 2000mm chord"
2. "Extrude 6000mm to create wing"
3. "Mirror across YZ plane to create both wings"
4. "Add 50mm fillet where wings meet fuselage"
```

**時間：** 約 5 分鐘

### 專案三：星型引擎

```
1. "Create one cylinder 50mm diameter, 100mm long"
2. "Circular pattern, 18 copies around X axis"
3. "Add central crankcase, 150mm diameter sphere"
4. "Fuse all cylinders and crankcase"
```

**時間：** 約 3 分鐘

---

## 下一步

### 繼續學習

- **[Corsair 工作流程](CORSAIR_MODELING_WORKFLOW.md)** - 完整飛機專案（100+ 小時 → 40 小時）
- **[API 參考](API_REFERENCE.md)** - 詳細工具文件
- **[貢獻](../CONTRIBUTING.md)** - 幫助改進 FreeCAD MCP

### 進階主題

- 鈑金設計
- 有限元素分析（FEM）
- TechDraw 工程圖
- 動畫
- 生成式設計

### 社群

- 分享您的專案
- 回報錯誤
- 建議新功能
- 貢獻程式碼

---

**祝您使用 FreeCAD MCP 設計愉快！** 🚀
