# DataFolderUtils.ipf 使用文档

## 概述
`DataFolderUtils.ipf` 提供了一系列用于操作 Igor Pro 中数据文件夹（DataFolder）的实用函数，包括创建、获取、遍历数据文件夹及其内容（如 wave 和子文件夹）等功能。

---

## 核心功能

### 1. 数据文件夹的创建与获取

#### 创建数据文件夹
```igor
// 创建数据文件夹（如果已存在且未设置覆盖，则报错）
DFREF dfr = DataFolder_create("root:newFolder")

// 创建数据文件夹（如果存在则覆盖）
DFREF dfr = DataFolder_create("root:newFolder", overwrite=1)
```

#### 获取数据文件夹的引用
```igor
// 获取已存在数据文件夹的引用（不存在则报错）
DFREF dfr = DataFolder_getDFRfromPath("root:existingFolder")
```

#### 创建或获取数据文件夹（安全方式）
```igor
// 如果数据文件夹不存在则创建，存在则返回引用
DFREF dfr = DataFolder_createOrGet("root:myFolder")
```

#### 创建或获取隐藏的数据文件夹引用
```igor
// 通过一个 wave 来存储 DFREF，用于持久化引用
// 如果 wave 不存在或存储的引用无效，则创建一个新的自由数据文件夹并存储
DFREF hiddenDFR = DataFolder_createOrGetHidden("root:hiddenRefWave")
```

### 2. 数据文件夹信息查询

#### 检查数据文件夹是否存在
```igor
// 检查一个 DFREF 是否指向一个存在的数据文件夹
Variable exists = isDataFolderExists(dfr)
```

#### 获取数据文件夹中的 wave 数量
```igor
Variable numWaves = DataFolder_countWaves(dfr)
```

#### 获取数据文件夹中的子文件夹数量
```igor
Variable numSubfolders = DataFolder_countSubfolders(dfr)
```

#### 获取当前数据文件夹路径
```igor
String currentPath = DataFolder_currentPath()
```

#### 获取数据文件夹的路径字符串
```igor
String path = DataFolder_getPath(dfr)
```

### 3. 遍历数据文件夹内容

#### 按索引获取 wave 名称
```igor
// 获取数据文件夹中第 i 个 wave 的完整路径
String waveName = DataFolder_getWaveNameByIndex(dfr, i)
```

#### 按索引获取子文件夹名称
```igor
// 获取数据文件夹中第 i 个子文件夹的完整路径
String dfName = DataFolder_getDFNameByIndex(dfr, i)
```

#### 按索引获取 wave 引用
```igor
// 获取数据文件夹中第 i 个 wave 的引用
Wave w = DataFolder_getWaveByIndex(dfr, i)
```

#### 按索引获取子文件夹引用
```igor
// 获取数据文件夹中第 i 个子文件夹的引用
DFREF subDFR = DataFolder_getDFByIndex(dfr, i)
```

#### 获取数据文件夹中所有 wave 的列表
```igor
// 返回一个字符串列表，包含数据文件夹中所有 wave 的完整路径
String waveList = DataFolder_getWaveList(dfr)
```

#### 获取数据文件夹中所有子文件夹的列表
```igor
// 返回一个字符串列表，包含数据文件夹中所有子文件夹的完整路径
String folderList = DataFolder_getSubfolderList(dfr)
```

---

## 使用示例

### 示例1：创建数据文件夹并添加 wave
```igor
// 创建数据文件夹
DFREF dfr = DataFolder_createOrGet("root:experiment1")

// 切换到该数据文件夹
SetDataFolder dfr

// 创建一个 wave
Make/O/N=100 myWave
```

### 示例2：遍历数据文件夹中的 wave
```igor
// 获取当前数据文件夹
DFREF dfr = GetDataFolderDFR()

// 获取 wave 数量
Variable numWaves = DataFolder_countWaves(dfr)

// 遍历并打印每个 wave 的名称
Variable i
for (i=0; i<numWaves; i+=1)
    String wName = DataFolder_getWaveNameByIndex(dfr, i)
    Print "Wave", i, ":", wName
endfor
```

### 示例3：递归遍历子文件夹
```igor
Function TraverseFolder(dfr)
    DFREF dfr
    
    // 处理当前文件夹的 wave
    String waveList = DataFolder_getWaveList(dfr)
    Print "Waves in folder:", DataFolder_getPath(dfr)
    Print waveList
    
    // 处理子文件夹
    String folderList = DataFolder_getSubfolderList(dfr)
    Variable numFolders = ItemsInList(folderList)
    for (i=0; i<numFolders; i+=1)
        String subFolderPath = StringFromList(i, folderList)
        DFREF subDFR = $subFolderPath
        TraverseFolder(subDFR)
    endfor
End

// 从根文件夹开始遍历
TraverseFolder(root:)
```

---

## 注意事项

1. **路径格式**：所有函数都要求使用完整路径（如 `"root:folder:subfolder"`）或有效的 `DFREF` 引用。
2. **隐藏引用**：`DataFolder_createOrGetHidden` 使用一个 wave 来存储 `DFREF`，这个 wave 必须存在于持久的数据文件夹中（如 `root`），否则在程序结束后引用会失效。
3. **错误处理**：部分函数在遇到错误时会终止程序（如 `Abort`），请确保在调用前检查路径是否存在（特别是 `DataFolder_create` 在没有设置 `overwrite` 时）。

--- 

## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `DataFolder_create` | 创建数据文件夹 | DFREF | `dfpath`: 路径, `[overwrite]`: 是否覆盖 |
| `DataFolder_getDFRfromPath` | 获取数据文件夹引用 | DFREF | `dfpath`: 路径 |
| `DataFolder_createOrGet` | 创建或获取数据文件夹 | DFREF | `dfpath`: 路径 |
| `DataFolder_createOrGetHidden` | 创建/获取隐藏数据文件夹引用 | DFREF | `refpath`: 引用wave路径 |
| `isDataFolderExists` | 检查数据文件夹是否存在 | Variable | `df_ref`: 数据文件夹引用 |
| `DataFolder_countWaves` | 获取wave数量 | Variable | `df_ref`: 数据文件夹引用 |
| `DataFolder_countSubfolders` | 获取子文件夹数量 | Variable | `df_ref`: 数据文件夹引用 |
| `DataFolder_currentPath` | 获取当前数据文件夹路径 | String | 无 |
| `DataFolder_getPath` | 获取数据文件夹路径 | String | `df_ref`: 数据文件夹引用 |
| `DataFolder_getWaveNameByIndex` | 按索引获取wave名称 | String | `df_ref`: 数据文件夹引用, `wave_idx`: 索引 |
| `DataFolder_getDFNameByIndex` | 按索引获取子文件夹名称 | String | `df_ref`: 数据文件夹引用, `df_idx`: 索引 |
| `DataFolder_getWaveByIndex` | 按索引获取wave引用 | Wave | `df_ref`: 数据文件夹引用, `wave_idx`: 索引 |
| `DataFolder_getDFByIndex` | 按索引获取子文件夹引用 | DFREF | `df_ref`: 数据文件夹引用, `df_idx`: 索引 |
| `DataFolder_getWaveList` | 获取所有wave列表 | String | `df_ref`: 数据文件夹引用 |
| `DataFolder_getSubfolderList` | 获取所有子文件夹列表 | String | `df_ref`: 数据文件夹引用 |
