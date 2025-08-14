# WaveUtils.ipf 使用文档

## 概述
`WaveUtils.ipf` 提供了一系列用于操作和处理 Igor Pro 中 wave 数据的实用函数，包括 wave 扩展、切片、数学运算、数据清理等功能。所有函数都支持一维和二维 wave 操作。

---

## 核心功能

### 1. Wave 扩展与维度管理
#### 添加行/列
```igor
// 添加单行 (返回新行索引)
newRow = Wave_appendRow(wave1)

// 添加多行 (返回第一个新行索引)
firstNewRow = Wave_appendRows(wave1, 5)

// 添加列
firstNewCol = Wave_appendColumns(wave2D, 3)

// 按比例扩展行数 (扩展因子10%)
firstNewRow = Wave_expandRows(wave1)
```

#### 删除行
```igor
// 删除指定行范围
Wave_removeRows(wave1, 10, 20) // 删除第10-20行
```

### 2. Wave 信息获取
```igor
// 获取维度信息
rows = Wave_getRowCount(wave1)
cols = Wave_getColumnCount(wave2D)
lastRow = Wave_getLastRowIndex(wave1)

// 获取路径和名称
path = Wave_getPath(wave1)      // "root:folder:wave1"
dfPath = Wave_getDF(wave1)      // "root:folder"
dfRef = Wave_getDFR(wave1)      // 数据文件夹引用

// 获取单位信息
dataUnits = Wave_getDataUnits(wave1)
rowUnits = Wave_getRowUnits(wave1)
```

### 3. Wave 切片与子集
```igor
// 按点索引切片
Wave waveSlice = Wave_getSlice(wave1, 50, 100)
Wave_saveSlice(wave1, 50, 100, "sliceWave")

// 按X值切片
Wave xSlice = Wave_getSliceByX(wave1, 0.5, 2.0)
Wave_saveSliceByX(wave1, 0.5, 2.0, "xSliceWave")

// 从图形光标获取切片
// (需要先设置光标A/B)
Wave_saveSliceFromGraph("graphSlice")
```

### 4. 数学运算
```igor
// 基本运算
Wave_subtract(waveA, waveB, "resultWave") // resultWave = waveA - waveB
addWaves(waveA, waveB)                   // waveB += waveA

// 积分计算
area = Wave_integrateWRT(wave1, baseline=0.1, start_pt=50, end_pt=100)

// 整流处理
Wave_rectifyFull(wave1, 0)     // 全波整流
Wave_rectifyHalf(wave1, 0)     // 半波整流

// 降采样 (x_interval=0.1, 窗口大小=0.05)
Wave_decimate(wave1, 0.1, 0.05, "decimatedWave")
```

### 5. 数据清理
```igor
// 处理NaN值
Wave_pruneNaN(waveWithNaNs, "cleanWave")  // 删除NaN点
indices = Wave_indexNonNaN(waveWithNaNs)  // 获取非NaN索引
avg = Wave_averageNonNaN(waveWithNaNs)    // 非NaN平均值

// 按值清理
Wave_pruneValue(wave1, prune_val=999, "cleanWave")

// 数值裁剪
Wave_clip(wave1, -1, 1, "clippedWave")     // 超限值设为边界
Wave_clipToNaN(wave1, -1, 1, "nanClipped") // 超限值设为NaN
```

### 6. 特殊操作
```igor
// 设置零点
Wave_setPointToZeroX(wave1, 50) // 使第50点对应X=0

// 单位设置
Wave_setDataUnits(wave1, "mV")
Wave_setRowUnits(wave1, "ms")
Wave_setRowDelta(wave1, 0.001)

// 2D wave索引转换
colIndex = Wave2D_getColumnIndex(wave2D, 123)
rowIndex = Wave2D_getRowIndex(wave2D, 123, colIndex)
```

### 7. 集合操作
```igor
// 并集和交集
Wave unionWave = Wave_union(waveA, waveB)
Wave intersectWave = Wave_intersect(waveA, waveB)

// 数值转列表
stringList = Wave_NumsToList(wave1)
```

### 8. 高级查找
```igor
// 查找变化点
changePoint = Wave_findNextChange(wave1, start_pt=100, tol=0.01)

// 查找连续相同值
consecPoint = Wave_findConsec(wave1, 5) // 连续5个相同值的起点
```

---

## 工具函数

### 比较与验证
```igor
// 比较wave是否相等
isEqual = isWavesEqual(waveA, waveB, tol=1e-10)
```

### 存储管理
```igor
// 安全存储wave
result = Wave_store(tempWave, "root:data:finalWave", overwrite=1)
```

---

## 使用技巧

1. **高效扩展**：
   ```igor
   // 批量添加行减少内存碎片
   Wave_expandRows(largeWave)
   ```

2. **切片元数据**：
   ```igor
   // 切片自动记录原始范围
   Wave slice = Wave_getSlice(wave1, 100, 200)
   string info = note(slice) // 包含"Slice=100,200"
   ```

3. **安全操作**：
   ```igor
   // 使用/FREE避免内存泄漏
   Duplicate/FREE wave1, tempWave
   Wave_rectifyFull(tempWave, 0)
   ```

4. **二维数据处理**：
   ```igor
   // 转换二维索引
   Wave rowIndices = Wave_convert2DToRowIndices(flatIndices, origWave)
   ```

> **提示**：所有函数都包含详细的错误检查，当参数无效时会安全失败或返回-1
