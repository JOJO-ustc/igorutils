# GraphUtils.ipf 使用文档

## 概述
`GraphUtils.ipf` 提供了操作 Igor Pro 图表的实用函数，包括创建图表、获取图表信息、管理轨迹（traces）和坐标轴等功能。

---

## 核心功能

### 1. 图表管理
```igor
// 获取所有图表列表
String graphs = Graph_getListAll()

// 获取最顶层图表名称
String topGraph = Graph_getTopName()

// 创建新图表（自动命名）
String newGraph = Graph_create()

// 创建新图表（指定名称）
String namedGraph = Graph_create("MyGraph")
```

### 2. 轨迹管理
```igor
// 获取图表所有轨迹
String traces = Graph_getTraceList()

// 获取指定坐标轴的轨迹
String leftTraces = Graph_getTraceList(yaxis="left")

// 获取可见轨迹
String visibleTraces = Graph_getVisibleTraceList()

// 获取轨迹对应的wave
String waveList = Graph_getWaveList()
```

### 3. 坐标轴管理
```igor
// 获取图表所有坐标轴
String axes = Graph_getAxisList()

// 获取垂直坐标轴
String vertAxes = Graph_getAxisList(type="vertical")

// 获取水平坐标轴
String horizAxes = Graph_getAxisList(type="horizontal")
```

### 4. 轨迹着色
```igor
// 使用调色板为轨迹着色
Graph_colorTraces("0,65535,32768") // 红、绿、蓝
```

---

## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| **图表管理** |  |  |  |
| `Graph_getListAll` | 获取所有图表列表 | String | 无 |
| `Graph_getTopName` | 获取最顶层图表名称 | String | 无 |
| `Graph_create` | 创建新图表 | String | `[graph_name]`: 图表名称 |
| **轨迹管理** |  |  |  |
| `GraphLine_createFromWave` | 从wave创建折线 | 无 | `wave_in`: 输入wave, `waveout_name`: 输出名称 |
| `Graph_getTraceList` | 获取轨迹列表 | String | `[graph_name]`: 图表名称, `[xaxis]`: X轴, `[yaxis]`: Y轴 |
| `Graph_getVisibleTraceList` | 获取可见轨迹列表 | String | `[graph_name]`: 图表名称, `[xaxis]`: X轴, `[yaxis]`: Y轴 |
| `Graph_getWaveList` | 获取轨迹wave列表 | String | `[graph_name]`: 图表名称, `[xaxis]`: X轴, `[yaxis]`: Y轴 |
| **坐标轴管理** |  |  |  |
| `Graph_getAxisList` | 获取坐标轴列表 | String | `graph_name`: 图表名称, `[type]`: 类型 ("left","right","top","bottom","vertical","horizontal") |
| **图表样式** |  |  |  |
| `Graph_colorTraces` | 为轨迹着色 | 无 | `palette`: 颜色值列表, `[graph_name]`: 图表名称, `[xaxis]`: X轴, `[yaxis]`: Y轴 |

---

## 使用示例

### 1. 创建图表并添加轨迹
```igor
// 创建新图表
String graphName = Graph_create("AnalysisResults")

// 生成示例数据
Make/O/N=100 wave1 = sin(x/10)
Make/O/N=100 wave2 = cos(x/10)

// 添加轨迹
AppendToGraph wave1
AppendToGraph wave2

// 自动着色轨迹
Graph_colorTraces("0;65535", graph_name=graphName) // 红和蓝
```

### 2. 分析图表内容
```igor
Function PrintGraphInfo(graphName)
    String graphName
    
    // 获取坐标轴信息
    String axes = Graph_getAxisList(graphName)
    Print "图表坐标轴: ", axes
    
    // 获取轨迹信息
    String traces = Graph_getTraceList(graphName)
    Print "轨迹列表: ", traces
    
    // 获取轨迹对应的wave
    String waves = Graph_getWaveList(graphName)
    Print "关联wave: ", waves
End
```

### 3. 批量处理多个图表
```igor
Function ProcessAllGraphs()
    String graphList = Graph_getListAll()
    Variable count = ItemsInList(graphList)
    
    Variable i
    for (i = 0; i < count; i += 1)
        String graphName = StringFromList(i, graphList)
        Print "处理图表: ", graphName
        
        // 为每个图表添加标题
        TextBox/W=$graphName/N=title "图表 " + num2str(i+1)
        
        // 为所有轨迹着色
        Graph_colorTraces("0;32768;65535", graph_name=graphName)
    endfor
End
```

### 4. 坐标轴特定操作
```igor
Function FormatLeftAxis(graphName)
    String graphName
    
    // 获取左侧坐标轴
    String leftAxes = Graph_getAxisList(graphName, type="left")
    
    // 格式化每个左侧坐标轴
    Variable i
    for (i = 0; i < ItemsInList(leftAxes); i += 1)
        String axis = StringFromList(i, leftAxes)
        ModifyGraph/W=$graphName axthick($axis)=2
        Label/W=$graphName $axis "信号强度 (a.u.)"
    endfor
End
```

### 5. 创建特定轨迹的副本
```igor
Function DuplicateTraces(graphName, yaxis)
    String graphName, yaxis
    
    // 获取指定Y轴的轨迹
    String traces = Graph_getTraceList(graphName, yaxis=yaxis)
    
    // 复制每个轨迹
    Variable i
    for (i = 0; i < ItemsInList(traces); i += 1)
        String trace = StringFromList(i, traces)
        Wave traceWave = TraceNameToWaveRef(graphName, trace)
        
        // 创建副本
        String newName = NameOfWave(traceWave) + "_copy"
        Duplicate/O traceWave, $newName
    endfor
End
```

### 6. 动态更新轨迹
```igor
Function UpdateGraphWithNewData()
    // 获取当前图表
    String graphName = Graph_getTopName()
    
    // 获取所有轨迹
    String traces = Graph_getTraceList(graphName)
    
    // 更新每个轨迹的数据
    Variable i
    for (i = 0; i < ItemsInList(traces); i += 1)
        String trace = StringFromList(i, traces)
        Wave traceWave = TraceNameToWaveRef(graphName, trace)
        
        // 应用处理函数
        ProcessWave(traceWave)
    endfor
End
```

---

## 高级用法

### 创建仪表板
```igor
Function CreateDashboard()
    // 创建主图表
    String mainGraph = Graph_create("Dashboard")
    
    // 添加多个数据源
    AppendToGraph/W=$mainGraph Data1
    AppendToGraph/W=$mainGraph Data2
    AppendToGraph/W=$mainGraph Data3
    
    // 自动着色
    Graph_colorTraces("0;32768;65535", graph_name=mainGraph)
    
    // 添加图例
    Legend/W=$mainGraph/C/N=text0 "Data1\\s('Data1')\\rData2\\s('Data2')\\rData3\\s('Data3')"
    
    // 格式化坐标轴
    ModifyGraph/W=$mainGraph axthick(left)=2
    Label/W=$mainGraph left "测量值"
    Label/W=$mainGraph bottom "时间 (s)"
End
```

### 自动化报告生成
```igor
Function GenerateReport()
    // 创建报告图表
    String reportGraph = Graph_create("AnalysisReport")
    
    // 添加关键数据
    AppendToGraph/W=$reportGraph ImportantData
    
    // 应用标准格式
    ApplyStandardFormat(reportGraph)
    
    // 保存为图片
    SavePICT/E=-5/O/WIN=$reportGraph as "Report.png"
End

Function ApplyStandardFormat(graphName)
    String graphName
    
    // 设置背景和网格
    ModifyGraph/W=$graphName gbRGB=(65535,65535,65535)
    ModifyGraph/W=$graphName grid(left)=1
    
    // 自动着色轨迹
    Graph_colorTraces("0;32768;65535", graph_name=graphName)
    
    // 添加标题
    TextBox/W=$graphName/N=title "实验报告"
    
    // 设置坐标轴标签
    Label/W=$graphName left "结果 (单位)"
    Label/W=$graphName bottom "样本编号"
End
```

### 数据比较视图
```igor
Function CreateComparisonView()
    // 创建对比图表
    String compGraph = Graph_create("DataComparison")
    
    // 添加对照组
    AppendToGraph/W=$compGraph ControlData
    ModifyGraph/W=$compGraph lsize(ControlData)=2
    
    // 添加实验组
    AppendToGraph/W=$compGraph ExperimentData
    ModifyGraph/W=$compGraph rgb(ExperimentData)=(0,0,65535)
    
    // 添加差异数据
    AppendToGraph/W=$compGraph DifferenceData
    ModifyGraph/W=$compGraph rgb(DifferenceData)=(65535,0,0)
    
    // 添加图例
    Legend/W=$compGraph/C/N=text0 "对照组\\s('ControlData')\\r实验组\\s('ExperimentData')\\r差异\\s('DifferenceData')"
End
```

这些功能使图表操作更加高效和自动化，特别适合数据分析、报告生成和科学可视化等场景。
