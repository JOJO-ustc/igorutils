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
# WaveUtils.ipf 函数参考表

## 基础操作

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `Wave_appendRow` | 添加单行 | Variable | `wave_in`: 输入 wave |
| `Wave_appendRows` | 添加多行 | Variable | `wave_in`: 输入 wave, `number_rows_to_add`: 要添加的行数 |
| `Wave_appendColumns` | 添加列 | Variable | `wave_in`: 输入 wave, `add_n`: 要添加的列数 |
| `Wave_appendToDimension` | 向指定维度添加元素 | Variable | `wave_in`: 输入 wave, `add_n`: 要添加的元素数, `dim_num`: 维度编号 (0-行,1-列) |
| `Wave_expandRows` | 按比例扩展行数 | Variable | `wave_in`: 输入 wave |
| `Wave_removeRows` | 移除行 | 无 | `wave_in`: 输入 wave, `start_i`: 起始行, `end_i`: 结束行 |
| `Wave_getLastRowIndex` | 获取最后一行索引 | Variable | `wave_in`: 输入 wave |
| `Wave_getRowCount` | 获取行数 | Variable | `wave_in`: 输入 wave |
| `Wave_getColumnCount` | 获取列数 | Variable | `wave_in`: 输入 wave |
| `Wave_getDimSize` | 获取维度大小 | Variable | `wave_in`: 输入 wave, `dim_num`: 维度编号 |
| `Wave_getUniqueName` | 获取唯一名称 | String | `base_name`: 基础名称 |
| `Wave_store` | 安全存储 wave | Variable | `wave_in`: 输入 wave, `path`: 存储路径, `[overwrite]`: 是否覆盖 |

## 元数据操作

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `Wave_getPath` | 获取完整路径 | String | `wave_in`: 输入 wave |
| `Wave_getDF` | 获取数据文件夹路径 | String | `wave_in`: 输入 wave |
| `Wave_getDFR` | 获取数据文件夹引用 | DFREF | `wave_in`: 输入 wave |
| `Wave_getDataUnits` | 获取数据单位 | String | `wave_in`: 输入 wave |
| `Wave_getRowUnits` | 获取行单位 | String | `wave_in`: 输入 wave |
| `Wave_setDataUnits` | 设置数据单位 | 无 | `wave_in`: 输入 wave, `new_units`: 新单位 |
| `Wave_setRowDelta` | 设置行间隔 | 无 | `wave_in`: 输入 wave, `new_delta`: 新间隔值 |
| `Wave_setRowUnits` | 设置行单位 | 无 | `wave_in`: 输入 wave, `new_units`: 新单位 |
| `Wave_setRowOffset` | 设置行偏移 | 无 | `wave_in`: 输入 wave, `new_offset`: 新偏移值 |
| `Wave_getRowDelta` | 获取行间隔 | Variable | `wave_in`: 输入 wave |
| `Wave_getRowOffset` | 获取行偏移 | Variable | `wave_in`: 输入 wave |

## 切片与子集

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `Wave_getSubrange` | 获取子范围 | Wave | `wave_in`: 输入 wave, `point_min`: 起始点, `point_max`: 结束点 |
| `Wave_getSlice` | 按点索引切片 | Wave | `wave_in`: 输入 wave, `start_pt`: 起始点, `end_pt`: 结束点 |
| `Wave_getSliceByX` | 按X值切片 | Wave | `wave_in`: 输入 wave, `start_x`: 起始X值, `end_x`: 结束X值 |
| `Wave_saveSlice` | 保存切片 | 无 | `wave_in`: 输入 wave, `start_pt`: 起始点, `end_pt`: 结束点, `waveout_name`: 输出名称 |
| `Wave_saveSliceByX` | 按X值保存切片 | 无 | `wave_in`: 输入 wave, `start_x`: 起始X值, `end_x`: 结束X值, `waveout_name`: 输出名称 |
| `Wave_saveSliceFromGraph` | 从图形光标保存切片 | 无 | `waveout_name`: 输出名称 |
| `Wave_setPointToZeroX` | 设置指定点为X零点 | 无 | `wave_in`: 输入 wave, `new_zero_pt`: 新零点位置 |

## 数学运算

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `addWaves` | 加法运算 | 无 | `waveA`: 输入 wave A, `waveB`: 输入 wave B |
| `addWaves_noNaNs` | 忽略NaN的加法 | 无 | `waveA`: 输入 wave A, `waveB`: 输入 wave B |
| `Wave_subtract` | 减法运算 | 无 | `waveA`: 输入 wave A, `waveB`: 输入 wave B, `waveout_name`: 输出名称 |
| `Wave_rectifyFull` | 全波整流 | 无 | `wave_in`: 输入 wave, `cut`: 截断值, `[pol]`: 极性 ("pos"/"neg") |
| `Wave_rectifyHalf` | 半波整流 | 无 | `wave_in`: 输入 wave, `cut`: 截断值, `[pol]`: 极性 ("pos"/"neg") |
| `Wave_integrateWRT` | 相对于基线的积分 | Variable | `wave_in`: 输入 wave, `baseline`: 基线值, `[start_pt]`: 起始点, `[end_pt]`: 结束点, `[polarity]`: 极性 |
| `Wave_decimate` | 降采样 | 无 | `wave_in`: 输入 wave, `x_interval`: 新间隔, `x_avg`: 平均窗口, `waveout_name`: 输出名称, `[no_ends]`: 是否忽略端点, `[start]`: 起始值, `[stop]`: 结束值 |
| `Wave_normMax` | 最大归一化 | 无 | `wave_in`: 输入 wave |

## 数据处理

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `Wave_clip` | 数值裁剪 | 无 | `wave_in`: 输入 wave, `min_y`: 最小值, `max_y`: 最大值, `waveout_name`: 输出名称 |
| `Wave_clipToNaN` | 裁剪为NaN | 无 | `wave_in`: 输入 wave, `min_y`: 最小值, `max_y`: 最大值, `waveout_name`: 输出名称 |
| `WaveSlice_clipToNaN` | 切片裁剪为NaN | 无 | `wave_in`: 输入 wave, `start_pt`: 起始点, `end_pt`: 结束点, `min_y`: 最小值, `max_y`: 最大值, `waveout_name`: 输出名称 |
| `Wave_pruneNaN` | 删除NaN点 | 无 | `wave_in`: 输入 wave, `outwave_name`: 输出名称 |
| `WaveW_pruneNaN` | 删除NaN点(返回wave) | Wave | `wave_in`: 输入 wave |
| `Wave_indexNonNaN` | 获取非NaN索引 | Wave | `wave_in`: 输入 wave |
| `Wave_pruneValue` | 删除特定值 | 无 | `wave_in`: 输入 wave, `prune_val`: 删除值, `outwave_name`: 输出名称 |
| `Wave_averageNonNaN` | 非NaN平均值 | Variable | `wave_in`: 输入 wave |
| `Wave_count` | 统计特定值数量 | Variable | `wave_in`: 输入 wave, `val`: 目标值 |

## 高级功能

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `Wave2D_getColumnIndex` | 2D转列索引 | Variable | `wave_in`: 2D wave, `onedim_index`: 一维索引 |
| `Wave2D_getRowIndex` | 2D转行索引 | Variable | `wave_in`: 2D wave, `onedim_index`: 一维索引, `col_index`: 列索引 |
| `Wave_convert2DToRowIndices` | 2D索引转行索引 | Wave | `wave_ids`: 索引wave, `wave_orig`: 原始wave |
| `Wave_getRangeBounds` | 获取范围边界 | 无 | `wave_in`: 输入 wave, `inc`: 增量, `waveout_name`: 输出名称, `[start_y]`: 起始Y值, `[end_y]`: 结束Y值, `[start_pt]`: 起始点, `[end_pt]`: 结束点 |
| `Wave_union` | wave并集 | Wave | `a`: wave A, `b`: wave B |
| `Wave_intersect` | wave交集 | Wave | `a`: wave A, `b`: wave B |
| `Wave_NumsToList` | 数值转列表 | String | `wave_in`: 输入 wave |
| `Wave_findNextChange` | 查找下一个变化点 | Variable | `wave_in`: 输入 wave, `[start_pt]`: 起始点, `[tol]`: 容差 |
| `Wave_findConsec` | 查找连续相同值 | Variable | `wave_in`: 输入 wave, `num`: 连续数量, `[start_pt]`: 起始点, `[tol]`: 容差 |
| `Wave_mean` | 点范围平均值 | Variable | `wvin`: 输入 wave, `pbeg`: 起始点, `pend`: 结束点 |
| `isWavesEqual` | 比较wave相等性 | Variable | `waveA`: wave A, `waveB`: wave B, `[tol]`: 容差 |
