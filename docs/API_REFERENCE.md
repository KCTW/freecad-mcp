# API 參考

FreeCAD MCP 所有工具的完整參考文件。

## 目錄

- [文件管理](#文件管理)
- [基本建模](#基本建模)
- [草圖工作流程](#草圖工作流程)
- [布林運算](#布林運算)
- [進階建模](#進階建模)
- [變換與陣列](#變換與陣列)
- [參考幾何](#參考幾何)
- [組裝操作](#組裝操作)
- [視覺回饋](#視覺回饋)

---

## 文件管理

### `create_document`

建立新的 FreeCAD 文件。

**參數：**
- `name` (str)：文件名稱

**回傳：**
- 成功/錯誤訊息

**範例：**
```json
{
  "name": "MyProject"
}
```

---

### `get_objects`

列出文件中的所有物件。

**參數：**
- `doc_name` (str)：文件名稱

**回傳：**
- 包含屬性的物件清單

**範例：**
```json
{
  "doc_name": "MyProject"
}
```

---

### `get_object`

取得特定物件的詳細資訊。

**參數：**
- `doc_name` (str)：文件名稱
- `obj_name` (str)：物件名稱

**回傳：**
- 物件屬性與詳細資訊

**範例：**
```json
{
  "doc_name": "MyProject",
  "obj_name": "Box001"
}
```

---

## 基本建模

### `create_object`

在 FreeCAD 中建立參數化物件。

**參數：**
- `doc_name` (str)：文件名稱
- `obj_type` (str)：物件類型（例如 "Part::Box"、"Part::Cylinder"）
- `obj_name` (str)：物件名稱
- `obj_properties` (dict，選填)：物件屬性
- `analysis_name` (str，選填)：FEM 分析名稱（如適用）

**支援的類型：**
- `Part::Box` - 長方體
- `Part::Cylinder` - 圓柱
- `Part::Sphere` - 球體
- `Part::Cone` - 圓錐
- `Part::Torus` - 圓環
- `Draft::Circle` - 2D 圓形
- `PartDesign::Body` - Part Design 本體
- `Fem::AnalysisPython` - FEM 分析
- 以及更多...

**範例：**
```json
{
  "doc_name": "MyProject",
  "obj_name": "MyCylinder",
  "obj_type": "Part::Cylinder",
  "obj_properties": {
    "Height": 100,
    "Radius": 20,
    "Placement": {
      "Base": {"x": 0, "y": 0, "z": 0}
    }
  }
}
```

---

### `edit_object`

修改現有物件的屬性。

**參數：**
- `doc_name` (str)：文件名稱
- `obj_name` (str)：物件名稱
- `obj_properties` (dict)：要修改的屬性

**範例：**
```json
{
  "doc_name": "MyProject",
  "obj_name": "MyCylinder",
  "obj_properties": {
    "Height": 150,
    "Radius": 25
  }
}
```

---

### `delete_object`

從文件中刪除物件。

**參數：**
- `doc_name` (str)：文件名稱
- `obj_name` (str)：物件名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "obj_name": "MyCylinder"
}
```

---

### `execute_code`

在 FreeCAD 中執行任意 Python 程式碼。

**參數：**
- `code` (str)：要執行的 Python 程式碼

**範例：**
```json
{
  "code": "import FreeCAD\nFreeCAD.Console.PrintMessage('Hello from Claude!')"
}
```

---

## 草圖工作流程

### `create_datum_plane_tool`

建立用於繪製草圖的參考平面。

**參數：**
- `doc_name` (str)：文件名稱
- `plane_name` (str)：平面名稱
- `alignment` (str)："xy"、"xz" 或 "yz"
- `offset` (float)：與原點的偏移量

**範例：**
```json
{
  "doc_name": "MyProject",
  "plane_name": "base_plane",
  "alignment": "xy",
  "offset": 0.0
}
```

---

### `create_sketch_on_plane_tool`

建立附加至基準平面的草圖。

**參數：**
- `doc_name` (str)：文件名稱
- `plane_name` (str)：基準平面名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "plane_name": "base_plane"
}
```

建立：`base_plane_sketch`

---

### `add_contour_to_sketch_tool`

在草圖中加入幾何元素。

**參數：**
- `doc_name` (str)：文件名稱
- `sketch_name` (str)：草圖名稱
- `geometry_elements` (list)：幾何元素清單
- `constraints` (list，選填)：約束清單
- `fix_first_point_to_origin` (bool)：將第一個點固定在原點

**幾何類型：**
- `point`：`{"type": "point", "x": 0, "y": 0}`
- `line`：`{"type": "line", "start": {"x": 0, "y": 0}, "end": {"x": 100, "y": 0}}`
- `arc`：`{"type": "arc", "center": {"x": 0, "y": 0}, "radius": 50, "start_angle": 0, "end_angle": 90}`
- `circle`：`{"type": "circle", "center": {"x": 0, "y": 0}, "radius": 25}`
- `bspline`：`{"type": "bspline", "points": [...], "degree": 3, "closed": false}`
- `ellipse`：`{"type": "ellipse", "center": {"x": 0, "y": 0}, "major_radius": 50, "minor_radius": 25, "angle": 0}`

**約束類型：**
- `coincident`：`{"type": "coincident", "geo1": 0, "point1": 2, "geo2": 1, "point2": 1}`
- `tangent`：`{"type": "tangent", "geo1": 0, "geo2": 1}`
- `distance`：`{"type": "distance", "geo1": 0, "point1": 1, "geo2": 0, "point2": 2, "value": 100}`
- `horizontal`：`{"type": "horizontal", "geo": 0}`
- `vertical`：`{"type": "vertical", "geo": 0}`
- `angle`：`{"type": "angle", "geo1": 0, "geo2": 1, "value": 90}`
- `fix`：`{"type": "fix", "geo": 0, "point": 1}`

**範例：**
```json
{
  "doc_name": "MyProject",
  "sketch_name": "base_plane_sketch",
  "geometry_elements": [
    {"type": "line", "start": {"x": 0, "y": 0}, "end": {"x": 100, "y": 0}},
    {"type": "line", "start": {"x": 100, "y": 0}, "end": {"x": 100, "y": 50}},
    {"type": "line", "start": {"x": 100, "y": 50}, "end": {"x": 0, "y": 50}},
    {"type": "line", "start": {"x": 0, "y": 50}, "end": {"x": 0, "y": 0}}
  ],
  "constraints": [
    {"type": "horizontal", "geo": 0},
    {"type": "vertical", "geo": 1}
  ]
}
```

---

### `extrude_sketch_bidirectional_tool`

擠出草圖以建立 3D 實體。

**參數：**
- `doc_name` (str)：文件名稱
- `sketch_name` (str)：草圖名稱
- `length_forward` (float)：向前擠出長度
- `length_backward` (float)：向後擠出長度
- `use_midplane` (bool)：對稱擠出

**範例：**
```json
{
  "doc_name": "MyProject",
  "sketch_name": "base_plane_sketch",
  "length_forward": 50.0,
  "length_backward": 0.0,
  "use_midplane": false
}
```

建立：`base_plane_solid`

---

## 布林運算

### `boolean_union_tool`

將多個實體融合為一個。

**參數：**
- `doc_name` (str)：文件名稱
- `base_object_name` (str)：基礎物件
- `tool_object_names` (list)：要融合的物件
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "base_object_name": "Box001",
  "tool_object_names": ["Cylinder001", "Sphere001"],
  "result_name": "Combined"
}
```

---

### `boolean_cut_tool`

從一個實體中減去另一個。

**參數：**
- `doc_name` (str)：文件名稱
- `base_object_name` (str)：基礎物件
- `tool_object_name` (str)：要減去的物件
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "base_object_name": "Box001",
  "tool_object_name": "Cylinder001",
  "result_name": "BoxWithHole"
}
```

---

### `boolean_intersection_tool`

僅保留兩個實體之間的共同體積。

**參數：**
- `doc_name` (str)：文件名稱
- `object1_name` (str)：第一個物件
- `object2_name` (str)：第二個物件
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "object1_name": "Sphere001",
  "object2_name": "Box001",
  "result_name": "Common"
}
```

---

## 進階建模

### `create_loft_tool`

在多個輪廓之間建立放樣。

**參數：**
- `doc_name` (str)：文件名稱
- `sketch_names` (list)：草圖名稱清單（輪廓）
- `result_name` (str)：結果名稱
- `solid` (bool)：建立實體（預設：True）
- `ruled` (bool)：直紋曲面（預設：False）

**範例：**
```json
{
  "doc_name": "MyProject",
  "sketch_names": ["Profile1", "Profile2", "Profile3"],
  "result_name": "Loft001",
  "solid": true,
  "ruled": false
}
```

---

### `create_revolve_tool`

將輪廓繞軸旋轉。

**參數：**
- `doc_name` (str)：文件名稱
- `sketch_name` (str)：輪廓草圖名稱
- `axis` (dict)：軸定義
- `angle` (float)：旋轉角度（預設：360）
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "sketch_name": "Profile",
  "axis": {
    "point": {"x": 0, "y": 0, "z": 0},
    "direction": {"x": 0, "y": 0, "z": 1}
  },
  "angle": 360,
  "result_name": "Revolve001"
}
```

---

### `create_sweep_tool`

沿路徑掃掠輪廓。

**參數：**
- `doc_name` (str)：文件名稱
- `profile_sketch` (str)：輪廓草圖名稱
- `path_sketch` (str)：路徑草圖名稱
- `result_name` (str)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "profile_sketch": "Circle",
  "path_sketch": "Spline",
  "result_name": "Sweep001"
}
```

---

### `add_fillet_tool`

在物件上加入圓角（圓滑邊緣）。

**參數：**
- `doc_name` (str)：文件名稱
- `object_name` (str)：物件名稱
- `edges` (list)：邊緣名稱清單
- `radius` (float)：圓角半徑
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "object_name": "Box001",
  "edges": ["Edge1", "Edge2", "Edge5"],
  "radius": 5.0,
  "result_name": "BoxRounded"
}
```

---

### `add_chamfer_tool`

在物件上加入倒角（斜切邊緣）。

**參數：**
- `doc_name` (str)：文件名稱
- `object_name` (str)：物件名稱
- `edges` (list)：邊緣名稱清單
- `distance` (float)：倒角距離
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "object_name": "Box001",
  "edges": ["Edge1", "Edge3"],
  "distance": 2.0,
  "result_name": "BoxChamfered"
}
```

---

### `shell_object_tool`

從實體建立薄殼。

**參數：**
- `doc_name` (str)：文件名稱
- `object_name` (str)：物件名稱
- `thickness` (float)：壁厚
- `faces_to_remove` (list，選填)：要移除的面（開啟薄殼）
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "object_name": "Box001",
  "thickness": 2.0,
  "faces_to_remove": ["Face6"],
  "result_name": "BoxShell"
}
```

---

## 變換與陣列

### `transform_object_tool`

使用平移和/或旋轉變換物件。

**參數：**
- `doc_name` (str)：文件名稱
- `obj_name` (str)：物件名稱
- `position` (dict，選填)：位置 {"x": 0, "y": 0, "z": 0}
- `rotation` (dict，選填)：旋轉 {"axis": {"x": 0, "y": 0, "z": 1}, "angle": 0}
- `relative` (bool)：相對變換

**範例：**
```json
{
  "doc_name": "MyProject",
  "obj_name": "Cylinder001",
  "position": {"x": 100, "y": 50, "z": 20},
  "rotation": {"axis": {"x": 0, "y": 0, "z": 1}, "angle": 45},
  "relative": false
}
```

---

### `mirror_object_tool`

沿平面鏡像物件。

**參數：**
- `doc_name` (str)：文件名稱
- `source_obj` (str)：要鏡像的物件
- `mirror_plane` (dict)：平面定義
- `result_name` (str，選填)：結果名稱
- `merge` (bool)：與原始物件合併

**範例：**
```json
{
  "doc_name": "MyProject",
  "source_obj": "Wing",
  "mirror_plane": {
    "base": {"x": 0, "y": 0, "z": 0},
    "normal": {"x": 1, "y": 0, "z": 0}
  },
  "merge": true,
  "result_name": "BothWings"
}
```

---

### `circular_pattern_tool`

建立環形陣列（極座標排列）。

**參數：**
- `doc_name` (str)：文件名稱
- `object_name` (str)：要陣列的物件
- `axis` (dict)：旋轉軸
- `count` (int)：實例數量
- `angle` (float)：總角度
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "object_name": "Cylinder",
  "axis": {
    "point": {"x": 0, "y": 0, "z": 0},
    "direction": {"x": 0, "y": 0, "z": 1}
  },
  "count": 6,
  "angle": 360,
  "result_name": "CircularPattern"
}
```

---

### `linear_pattern_tool`

建立線性陣列（矩形排列）。

**參數：**
- `doc_name` (str)：文件名稱
- `object_name` (str)：要陣列的物件
- `direction` (dict)：陣列方向
- `spacing` (float)：實例之間的距離
- `count` (int)：實例數量
- `result_name` (str，選填)：結果名稱

**範例：**
```json
{
  "doc_name": "MyProject",
  "object_name": "Hole",
  "direction": {"x": 1, "y": 0, "z": 0},
  "spacing": 50,
  "count": 5,
  "result_name": "HoleArray"
}
```

---

## 參考幾何

### `create_reference_plane_tool`

使用各種定義模式建立基準平面。

**參數：**
- `doc_name` (str)：文件名稱
- `plane_name` (str)：平面名稱
- `definition` (dict)：平面定義

**定義模式：**
- 偏移：`{"mode": "offset", "plane": "XY", "offset": 50}`
- 三點：`{"mode": "3points", "p1": {...}, "p2": {...}, "p3": {...}}`
- 點-法線：`{"mode": "point_normal", "point": {...}, "normal": {...}}`

**範例：**
```json
{
  "doc_name": "MyProject",
  "plane_name": "CustomPlane",
  "definition": {
    "mode": "point_normal",
    "point": {"x": 0, "y": 0, "z": 100},
    "normal": {"x": 0.2, "y": 0, "z": 1}
  }
}
```

---

### `import_airfoil_profile_tool`

匯入 NACA 翼型輪廓。

**參數：**
- `doc_name` (str)：文件名稱
- `sketch_name` (str)：草圖名稱
- `naca_code` (str)：NACA 代碼（例如 "2412"、"0012"）
- `chord_length` (float)：弦長（單位 mm）
- `position` (dict，選填)：位置

**範例：**
```json
{
  "doc_name": "MyProject",
  "sketch_name": "WingProfile",
  "naca_code": "2412",
  "chord_length": 2000,
  "position": {"x": 0, "y": 0, "z": 0}
}
```

---

### `import_dxf_tool`

將 DXF 檔案匯入草圖。

**參數：**
- `doc_name` (str)：文件名稱
- `file_path` (str)：DXF 檔案路徑
- `sketch_name` (str)：草圖名稱
- `scale` (float)：縮放比例

**範例：**
```json
{
  "doc_name": "MyProject",
  "file_path": "C:/drawings/part.dxf",
  "sketch_name": "ImportedProfile",
  "scale": 1.0
}
```

---

## 組裝操作

完整的組裝文件請參閱 [ASSEMBLY.md](ASSEMBLY.md)。

### Assembly3 工具
- `create_assembly3_tool`
- `add_part_to_assembly3_tool`
- `add_assembly3_constraint_tool`
- `solve_assembly3_tool`
- `list_assembly3_constraints_tool`
- `delete_assembly3_constraint_tool`
- `modify_assembly3_constraint_tool`

### Assembly4 工具
- `create_assembly4_tool`
- `create_lcs_assembly4_tool`
- `insert_part_assembly4_tool`
- `attach_lcs_to_geometry_tool`
- `list_assembly4_lcs_tool`
- `delete_lcs_assembly4_tool`
- `modify_lcs_assembly4_tool`

### 通用工具
- `list_assembly_parts_tool`
- `export_assembly_tool`
- `calculate_assembly_mass_tool`
- `generate_bom_tool`
- `get_assembly_properties_tool`

---

## 視覺回饋

### `get_view`

從特定視角取得截圖。

**參數：**
- `view_name` (str)："Isometric"、"Front"、"Top"、"Right"、"Back"、"Left"、"Bottom"、"Dimetric"、"Trimetric"

**範例：**
```json
{
  "view_name": "Isometric"
}
```

---

### `insert_part_from_library`

從 FreeCAD 零件庫插入零件。

**參數：**
- `relative_path` (str)：零件庫中的相對路徑

**範例：**
```json
{
  "relative_path": "Fasteners/Bolts/ISO4017_M8x40.FCStd"
}
```

---

### `get_parts_list`

列出零件庫中可用的零件。

**回傳：**
- 可用零件清單

---

## 錯誤處理

所有工具回傳一致的回應格式：

**成功：**
```json
{
  "success": true,
  "message": "Operation completed",
  "screenshot": "base64_image_data"
}
```

**錯誤：**
```json
{
  "success": false,
  "error": "Error description"
}
```

---

## 最佳實踐

1. **在引用物件前**，務必使用 `get_objects` 確認物件名稱
2. **使用描述性名稱**為建立的物件命名
3. **透過截圖**驗證座標
4. **在 FreeCAD 中**增量儲存
5. **先使用簡單幾何**測試操作

---

**另請參閱：**
- [使用指南](USER_GUIDE.md) - 功能教學
- [快速入門](QUICKSTART.md) - 設定指南
- [Corsair 工作流程](CORSAIR_MODELING_WORKFLOW.md) - 實際範例
