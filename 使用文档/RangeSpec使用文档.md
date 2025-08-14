# RangeSpec.ipf 使用文档

## 概述
`RangeSpec.ipf` 提供了处理数字范围规范的实用函数，支持将简洁的范围描述字符串（如 "1-5:2"）扩展为数字列表（如 "1,3,5"）。这种格式特别适合需要指定一系列数字的场景，如数据选择、参数扫描等。

---

## 核心功能

### 范围规范格式
范围规范字符串支持以下格式：
- **单个数字**：`5` → `5`
- **连续范围**：`1-4` → `1,2,3,4`
- **带步长的范围**：`1-5:2` → `1,3,5`
- **降序范围**：`4-1:-1` → `4,3,2,1`
- **多重范围**：`1-3,5-7` → `1,2,3,5,6,7`
- **重叠范围自动合并**：`1-4,3-6` → `1,2,3,4,5,6`

### 扩展范围规范
```igor
// 扩展范围规范为数字列表
String numbers = RangeSpec_expand("1-5:2") // 返回 "1,3,5"
```

---

## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `RangeSpec_expand` | 扩展范围规范为数字列表 | String | `range_spec`: 范围规范字符串 |

---

## 使用示例

### 基本范围扩展
```igor
// 单个数字
String single = RangeSpec_expand("5") // "5"

// 连续范围
String range = RangeSpec_expand("1-4") // "1,2,3,4"

// 带步长的范围
String stepped = RangeSpec_expand("1-5:2") // "1,3,5"

// 降序范围
String descending = RangeSpec_expand("4-1:-1") // "4,3,2,1"
```

### 多重范围处理
```igor
// 不连续范围
String multiple = RangeSpec_expand("1-3,5-7") // "1,2,3,5,6,7"

// 重叠范围自动合并
String overlapping = RangeSpec_expand("1-4,3-6") // "1,2,3,4,5,6"
```

### 实际应用场景
```igor
// 选择数据点进行分析
Function AnalyzeSelectedPoints(pointSpec)
    String pointSpec
    
    // 扩展点规范
    String points = RangeSpec_expand(pointSpec)
    Variable count = ItemsInList(points)
    
    // 处理每个点
    Variable i
    for (i = 0; i < count; i += 1)
        Variable point = str2num(StringFromList(i, points))
        ProcessDataPoint(point)
    endfor
End

// 使用示例
AnalyzeSelectedPoints("10-20:2,25,30-35") // 处理点 10,12,14,16,18,20,25,30,31,32,33,34,35
```

```igor
// 批量创建wave
Function CreateWavesForExperiment(waveSpec)
    String waveSpec
    
    // 扩展wave编号
    String waveNums = RangeSpec_expand(waveSpec)
    Variable count = ItemsInList(waveNums)
    
    // 创建wave
    Variable i
    for (i = 0; i < count; i += 1)
        String waveName = "data_" + StringFromList(i, waveNums)
        Make/O/N=100 $waveName
    endfor
End

// 使用示例
CreateWavesForExperiment("1-5") // 创建 data_1 到 data_5
```

```igor
// 参数扫描
Function RunParameterScan(paramSpec)
    String paramSpec
    
    // 扩展参数值
    String params = RangeSpec_expand(paramSpec)
    Variable count = ItemsInList(params)
    
    // 执行扫描
    Variable i
    for (i = 0; i < count; i += 1)
        Variable value = str2num(StringFromList(i, params))
        SetParameter(value)
        RunExperiment()
    endfor
End

// 使用示例
RunParameterScan("0.1-1.0:0.2") // 扫描参数值 0.1, 0.3, 0.5, 0.7, 0.9
```

---

## 内部函数说明

虽然以下函数不直接对外使用，但了解它们有助于理解模块工作原理：

### `RangeSpec_expandSingle`
- **功能**：处理单个范围规范（不包含逗号）
- **参数**：`range_str` - 单个范围字符串
- **返回**：扩展后的数字列表

### `RangeSpec_splitParts`
- **功能**：解析范围规范的组成部分
- **参数**：
  - `range_str` - 范围字符串
  - `num_start` - 起始值（引用参数）
  - `num_end` - 结束值（引用参数）
  - `step` - 步长（引用参数）

### `expandRange`
- **功能**：根据起止值和步长生成数字序列
- **参数**：
  - `num_start` - 起始值
  - `num_end` - 结束值
  - `[step]` - 步长（默认1）

---

## 高级用法

### 动态生成实验参数
```igor
Function GenerateExperimentParameters()
    // 温度范围：25°C 到 30°C，步长 1°C
    String temperatures = RangeSpec_expand("25-30")
    
    // 浓度范围：0.1M 到 1.0M，步长 0.2M
    String concentrations = RangeSpec_expand("0.1-1.0:0.2")
    
    // 生成所有组合
    Variable tempCount = ItemsInList(temperatures)
    Variable concCount = ItemsInList(concentrations)
    
    String allParams = ""
    Variable t, c
    for (t = 0; t < tempCount; t += 1)
        for (c = 0; c < concCount; c += 1)
            String paramSet = StringFromList(t, temperatures) + "C_" + StringFromList(c, concentrations) + "M"
            allParams = List_addItem(allParams, paramSet)
        endfor
    endfor
    
    return allParams
End

// 返回示例: "25C_0.1M;25C_0.3M;...;30C_0.9M"
```

### 复杂范围组合
```igor
// 组合多个范围规范
String complexSpec = RangeSpec_expand("1-10:3,15,20-25:2,30-28:-1")
// 结果: "1,4,7,10,15,20,22,24,30,29,28"
```

### 数据子集选择
```igor
// 选择数据子集进行分析
Function/WAVE SelectDataSubset(waveIn, pointSpec)
    Wave waveIn
    String pointSpec
    
    // 获取点索引
    String points = RangeSpec_expand(pointSpec)
    Variable count = ItemsInList(points)
    
    // 创建子集wave
    Make/FREE/N=(count) subset
    Variable i
    for (i = 0; i < count; i += 1)
        subset[i] = waveIn[str2num(StringFromList(i, points))]
    endfor
    
    return subset
End
```

---

## 注意事项

1. **整数处理**：范围规范只支持整数，不支持浮点数
2. **步长方向**：步长值必须与范围方向一致（正数范围用正步长，负数范围用负步长）
3. **性能考虑**：对于非常大的范围（>10,000），使用循环可能更高效
4. **唯一值保证**：结果列表自动去重，即使范围有重叠

这些功能使范围规范成为处理数字序列的强大工具，特别适用于实验参数设置、数据选择和批处理操作等场景。
