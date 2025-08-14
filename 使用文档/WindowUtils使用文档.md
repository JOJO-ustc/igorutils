# WindowUtils.ipf 使用文档

## 概述
`WindowUtils.ipf` 提供了处理 Igor Pro 窗口（包括面板、图表等）的实用函数，主要用于窗口名称解析和窗口列表获取。

---

## 核心功能

### 1. 窗口名称解析
```igor
// 获取窗口的父窗口名称
String parentName = Window_getParentName("Graph0#AxisPanel")

// 对于没有父窗口的窗口（或相对路径）
String noParent = Window_getParentName("Graph0") // 返回空字符串
```

### 2. 获取窗口列表
```igor
// 获取所有窗口的列表
String allWindows = Window_getListAll()
// 返回格式: "Graph0;Graph1;Panel0;Table1;..."
```

---



## 使用说明

### 窗口名称格式
Igor Pro 使用以下格式表示子窗口：
```
主窗口名称#子窗口名称
```
例如：
- `Graph0` - 主图表窗口
- `Graph0#AxisPanel` - 图表窗口中的坐标轴面板子窗口

### Window_getParentName 工作原理
函数通过查找窗口名称中的 `#` 分隔符来确定父窗口：
1. 从名称末尾向前搜索最后一个 `#`
2. 再向前搜索倒数第二个 `#`
3. 返回两个 `#` 之间的部分作为父窗口名称

**示例解析**：
```igor
Window_getParentName("MainPanel#SubPanel#Control") 
// 返回 "SubPanel"
```

### 典型应用场景
1. **动态创建控件**：
```igor
Function CreateControl(winName, ctrlName)
    String winName, ctrlName
    
    // 确保控件创建在正确的父窗口中
    String parent = Window_getParentName(winName)
    if (strlen(parent) == 0)
        parent = winName
    endif
    
    Button $ctrlName parent=$parent, ...
End
```

2. **窗口关系验证**：
```igor
Function isChildWindow(childWin, parentWin)
    String childWin, parentWin
    return cmpstr(Window_getParentName(childWin), parentWin) == 0
End
```

3. **批量处理窗口**：
```igor
// 关闭所有图表窗口
String allWins = Window_getListAll()
Variable i
for(i=0; i<ItemsInList(allWins); i+=1)
    String win = StringFromList(i, allWins)
    if (stringmatch(win, "Graph*"))
        DoWindow/K $win
    endif
endfor
```

---

## 注意事项
1. 当处理多级子窗口时（如 `WinA#WinB#WinC`），函数只返回直接父窗口（`WinB`）
2. 对于没有 `#` 符号的窗口名称，函数返回空字符串
3. `Window_getListAll()` 返回当前内存中所有窗口的名称列表，包括隐藏窗口
## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `Window_getParentName` | 获取窗口的父窗口名称 | String | `window_name`: 窗口名称 |
| `Window_getListAll` | 获取所有窗口的列表 | String | 无 |

---
