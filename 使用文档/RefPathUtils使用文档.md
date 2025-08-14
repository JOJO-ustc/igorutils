# RefPathUtils.ipf 使用文档

## 概述
`RefPathUtils.ipf` 提供了处理 Igor Pro 对象引用路径的实用函数，主要用于解析各种形式的路径（绝对路径、相对路径、对象名）并转换为完整的绝对路径。

---

## 核心功能

### 1. 路径解析
```igor
// 解析各种路径格式为完整路径
String fullPath = RefPath_resolve(":subfolder:wave1") 
// 当前文件夹为 root:experiment 时返回 "root:experiment:subfolder:wave1"

fullPath = RefPath_resolve("wave2") 
// 返回 "root:experiment:wave2"（当前文件夹下）

fullPath = RefPath_resolve("root:data:wave3") 
// 返回 "root:data:wave3"（绝对路径）
```

### 2. 路径类型检查
```igor
// 检查路径类型
Variable isAbs = RefPath_isFull("root:data") // 1 (true)
Variable isRel = RefPath_isRelative(":wave") // 1 (true)
Variable isObj = RefPath_isObjName("wave5")  // 1 (true)
```

### 3. 路径格式检查
```igor
// 检查路径是否以冒号结尾
Variable hasColon = RefPath_hasTrailingColon("root:folder:") // 1 (true)
```

---

## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `RefPath_resolve` | 解析路径为完整路径 | String | `rp`: 路径字符串 |
| `RefPath_isFull` | 检查是否为绝对路径 | Variable | `rp`: 路径字符串 |
| `RefPath_isRelative` | 检查是否为相对路径 | Variable | `rp`: 路径字符串 |
| `RefPath_isObjName` | 检查是否为对象名 | Variable | `rp`: 路径字符串 |
| `RefPath_handleBacks` | 处理返回符路径（内部） | String | `rp`: 路径字符串 |
| `RefPath_hasTrailingColon` | 检查路径是否以冒号结尾 | Variable | `rp`: 路径字符串 |

---

## 使用说明

### 路径解析规则
1. **绝对路径**：以`root:`开头，直接返回
2. **相对路径**：以冒号开头，转换为当前文件夹下的路径
3. **对象名**：直接附加到当前文件夹路径
4. **返回符处理**：`::`表示返回上一级目录
   - `root:folder1::folder2` → `root:folder2`
   - `root:folder1:::folder3` → `root:folder3`（返回两级）

### 示例应用

#### 安全访问数据对象
```igor
Function Wave SafeGetWave(path)
    String path
    String fullPath = RefPath_resolve(path)
    return $fullPath
End

// 使用示例
Wave w = SafeGetWave(":sensors:tempData")
```

#### 动态创建数据文件夹
```igor
Function CreateFolderAtPath(path)
    String path
    String fullPath = RefPath_resolve(path)
    
    // 确保路径以冒号结尾
    if (!RefPath_hasTrailingColon(fullPath))
        fullPath += ":"
    endif
    
    NewDataFolder/S $fullPath
End

// 使用示例
CreateFolderAtPath("::results") // 在上级目录创建results文件夹
```

#### 处理用户输入路径
```igor
Function ProcessUserPath(userInput)
    String userInput
    
    // 解析路径
    String resolvedPath = RefPath_resolve(userInput)
    
    // 检查路径类型
    if (RefPath_isFull(resolvedPath))
        Print "绝对路径:", resolvedPath
    elseif (RefPath_isRelative(userInput)) // 注意：使用原始输入判断
        Print "相对路径解析为:", resolvedPath
    else
        Print "对象名解析为:", resolvedPath
    endif
End
```

#### 跨文件夹数据操作
```igor
Function CopyWaveBetweenFolders(srcPath, destPath)
    String srcPath, destPath
    
    // 解析路径
    String srcFull = RefPath_resolve(srcPath)
    String destFull = RefPath_resolve(destPath)
    
    // 获取wave名称
    String waveName = RefPath_getObjectName(srcFull)
    
    // 复制wave
    Wave srcWave = $srcFull
    Duplicate/O srcWave, $(destFull + waveName)
End

// 辅助函数：从路径中提取对象名
Function/S RefPath_getObjectName(fullPath)
    String fullPath
    // 移除尾部冒号（如果有）
    if (RefPath_hasTrailingColon(fullPath))
        fullPath = fullPath[0, strlen(fullPath)-2]
    endif
    // 返回最后一个冒号后的部分
    Variable lastColon = strsearch(fullPath, ":", Inf, 1)
    return fullPath[lastColon+1, Inf]
End
```

#### 路径导航系统
```igor
Function/S NavigatePath(currentPath, command)
    String currentPath, command
    
    // 处理导航命令
    if (cmpstr(command, "..") == 0)
        // 返回上一级
        return RefPath_resolve(currentPath + "::")
    elseif (cmpstr(command, "~") == 0)
        // 返回根目录
        return "root:"
    else
        // 进入子文件夹
        String newPath = currentPath + command + ":"
        return RefPath_resolve(newPath)
    endif
End

// 使用示例
String current = "root:experiment:"
current = NavigatePath(current, "data") // "root:experiment:data:"
current = NavigatePath(current, "..")   // "root:experiment:"
```

---

## 高级用法

### 路径批处理
```igor
Function ProcessMultiplePaths(pathList)
    String pathList
    
    Variable count = List_getLength(pathList)
    Variable i
    for (i = 0; i < count; i += 1)
        String path = List_getItem(pathList, i)
        String fullPath = RefPath_resolve(path)
        
        // 处理路径指向的对象
        if (DataFolderExists(fullPath))
            ProcessDataFolder(fullPath)
        elseif (WaveExists($fullPath))
            ProcessWave($fullPath)
        endif
    endfor
End
```

### 路径重写工具
```igor
Function/S RewritePath(oldPath, newBase)
    String oldPath, newBase
    
    // 解析旧路径
    String resolvedOld = RefPath_resolve(oldPath)
    
    // 提取对象名
    String objName = RefPath_getObjectName(resolvedOld)
    
    // 创建新路径
    return RefPath_resolve(newBase) + objName
End

// 使用示例
String newPath = RewritePath("root:old:data", "root:new:experiment")
// 返回 "root:new:experiment:data"
```

### 路径验证系统
```igor
Function ValidatePath(path)
    String path
    
    // 解析路径
    String resolved = RefPath_resolve(path)
    
    // 检查路径格式
    if (!RefPath_hasTrailingColon(resolved))
        // 对象路径
        if (!WaveExists($resolved) && !Exists(resolved))
            return 0 // 无效对象
        endif
    else
        // 文件夹路径
        if (!DataFolderExists(resolved))
            return 0 // 无效文件夹
        endif
    endif
    return 1 // 有效路径
End
```

---

## 注意事项

1. **路径存在性**：`RefPath_resolve` 不检查路径是否存在，只进行语法解析
2. **返回符限制**：过多的返回符（如`root::::`）会解析为根目录`root:`
3. **对象类型**：解析后的路径可能指向wave、变量或数据文件夹
4. **尾部冒号**：数据文件夹路径应以冒号结尾（如`root:folder:`）
5. **性能考虑**：频繁的路径解析可能影响性能，建议缓存结果

这些功能使路径操作更加灵活可靠，特别适合需要动态访问不同数据位置的应用场景。
