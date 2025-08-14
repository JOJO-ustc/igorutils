# FunctionInfoUtils.ipf 使用文档

## 概述
`FunctionInfoUtils.ipf` 提供了获取 Igor Pro 函数信息的实用工具，包括函数存在性检查、文档字符串提取和函数位置信息获取。

---

## 核心功能

### 1. 函数存在性检查
```igor
// 检查函数是否存在
Variable exists = isFunctionExists("MyFunction")
```

### 2. 获取函数文档字符串
```igor
// 获取函数签名前的文档注释
String preDoc = Func_getDocString("MyFunction")

// 获取函数签名后的文档注释
String postDoc = Func_getPostDocString("MyFunction")
```

### 3. 获取函数位置信息
```igor
// 获取函数所在的文件名
String filename = Func_getFilename("MyFunction")

// 获取函数在文件中的行号
Variable lineNum = Func_getLineNumber("MyFunction")
```

---

## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `isFunctionExists` | 检查函数是否存在 | Variable | `in_funcname`: 函数名称 |
| `Func_getDocString` | 获取函数签名前的文档注释 | String | `funcname`: 函数名称 |
| `Func_getPostDocString` | 获取函数签名后的文档注释 | String | `funcname`: 函数名称 |
| `Func_getFilename` | 获取函数所在的文件名 | String | `funcname`: 函数名称 |
| `Func_getLineNumber` | 获取函数在文件中的行号 | Variable | `funcname`: 函数名称 |

---

## 使用说明

### 函数文档字符串
Igor Pro 函数可以包含两种类型的文档注释：
1. **签名前文档**：位于函数签名之前
2. **签名后文档**：位于函数签名之后

**示例函数**：
```igor
// 这是签名前文档
// 可以包含多行注释
Function MyFunction()
    // 这是签名后文档
    // 也支持多行注释
    
    // 函数实现代码...
End
```

### 函数位置信息
`Func_getFilename` 和 `Func_getLineNumber` 使用 Igor Pro 内置的 `FunctionInfo()` 函数获取函数的元数据信息。

返回的信息示例：
```igor
filename = "MyProcedures.ipf"
lineNum = 42
```

### 典型应用场景

#### 自动化文档生成
```igor
Function GenerateFunctionDocs()
    String funcList = FunctionList("*", ";", "")
    Variable count = ItemsInList(funcList)
    
    for (i = 0; i < count; i += 1)
        String funcName = StringFromList(i, funcList)
        String doc = Func_getPostDocString(funcName)
        String filename = Func_getFilename(funcName)
        Variable lineNum = Func_getLineNumber(funcName)
        
        Printf "函数: %s\r", funcName
        Printf "文件: %s (行号: %d)\r", filename, lineNum
        Printf "文档:\r%s\r", doc
        Printf "----------------\r"
    endfor
End
```

#### 函数验证系统
```igor
Function ValidateFunctions()
    String requiredFuncs = "ProcessData;AnalyzeResults;GenerateReport"
    Variable count = ItemsInList(requiredFuncs)
    
    for (i = 0; i < count; i += 1)
        String funcName = StringFromList(i, requiredFuncs)
        if (!isFunctionExists(funcName))
            String errMsg
            sprintf errMsg, "缺少必需函数: %s", funcName
            Abort errMsg
        endif
    endfor
End
```

#### 函数导航工具
```igor
Function GoToFunction(funcName)
    String funcName
    
    if (!isFunctionExists(funcName))
        Print "函数不存在:", funcName
        return
    endif
    
    String filename = Func_getFilename(funcName)
    Variable lineNum = Func_getLineNumber(funcName)
    
    // 打开包含函数的文件
    OpenNotebook filename
    // 跳转到函数位置
    Notebook $filename selection={lineNum, 0, lineNum, 0}
End
```

---

## 注意事项

1. **函数可见性**：只能获取当前编译范围内的函数信息
2. **文档注释格式**：
   - 必须使用 `//` 注释格式
   - 注释必须紧邻函数签名（无空行）
   - 支持多行注释
3. **内置函数**：无法获取 Igor Pro 内置函数的信息
4. **行号准确性**：
   - `Func_getLineNumber` 返回函数签名的起始行号
   - 如果函数定义前有空行，实际行号可能不同

---

## 高级用法

### 自定义文档解析
```igor
Function/S ParseFunctionParameters(funcName)
    String funcName
    
    // 获取签名后文档
    String doc = Func_getPostDocString(funcName)
    
    // 解析参数文档
    String paramRegex = "\\s*@param\\s+(\\w+)\\s+(.*)"
    String paramList = String_findRegex(doc, paramRegex)
    
    String result = ""
    Variable count = ItemsInList(paramList)
    for (i = 0; i < count; i += 1)
        String paramInfo = StringFromList(i, paramList)
        // paramInfo 格式: "参数名 描述"
        result += paramInfo + "\r"
    endfor
    
    return result
End
```

### 函数依赖分析
```igor
Function AnalyzeFunctionDependencies(funcName)
    String funcName
    
    if (!isFunctionExists(funcName))
        Print "函数不存在:", funcName
        return
    endif
    
    // 获取函数代码
    String code = ProcedureText(funcName)
    
    // 解析调用的函数
    String funcCallRegex = "\\b([A-Za-z]\\w*)\\s*\\("
    String calledFuncs = String_findRegex(code, funcCallRegex)
    
    Printf "函数 %s 调用了以下函数:\r", funcName
    Print calledFuncs
End
```

### 文档质量检查
```igor
Function CheckDocumentationQuality()
    String funcList = FunctionList("*", ";", "")
    Variable count = ItemsInList(funcList)
    Variable undocumented = 0
    
    for (i = 0; i < count; i += 1)
        String funcName = StringFromList(i, funcList)
        String doc = Func_getPostDocString(funcName)
        
        if (strlen(doc) == 0)
            Print "未文档化的函数:", funcName
            undocumented += 1
        endif
    endfor
    
    Printf "\r总计: %d 个函数中 %d 个未文档化 (%.1f%%)", 
            count, undocumented, 100*undocumented/count
End
```
