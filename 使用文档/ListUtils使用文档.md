# ListUtils.ipf 使用文档

## 概述
`ListUtils.ipf` 提供了一系列处理字符串列表的实用函数，支持列表的创建、修改、查询和转换操作。列表使用分号 (`;`) 作为默认分隔符。

---

## 核心功能

### 1. 基本列表操作
```igor
// 获取列表长度
Variable len = List_getLength("a;b;c") // 3

// 添加项目
String newList = List_addItem("a;b", "c") // "a;b;c"

// 插入项目
String inserted = List_insertItem("a;b;d", "c", 2) // "a;b;c;d"

// 获取项目
String item = List_getItem("a;b;c", 1) // "b"

// 移除项目
String removed = List_removeItem("a;b;c", 1) // "a;c"
```

### 2. 高级列表操作
```igor
// 弹出最后一个项目
String list = "a;b;c"
String last = List_pop(list) // "c" (list变为"a;b")

// 列表去重
String unique = List_unique("a;b;a;c") // "a;b;c"

// 列表反转
String reversed = List_reverse("a;b;c") // "c;b;a"

// 列表排序
String sorted = List_sort("c;a;b") // "a;b;c"

// 数字列表排序
String numSorted = List_sortNumeric("3;1;2") // "1;2;3"
```

### 3. 列表转换与比较
```igor
// 转换分隔符
String commaList = List_convertSeparator("a;b;c", ";", new_sep=",") // "a,b,c"

// 列表合并
String merged = List_extend("a;b", "c;d") // "a;b;c;d"

// 列表差集
String diff = List_difference("a;b;c", "b;d") // "a;c"

// 列表切片
String slice = List_getSlice("a;b;c;d;e", 1, 3) // "b;c;d"
```

### 4. 列表压缩与扩展
```igor
// 列表压缩
String compact = List_compact("file1.txt;file2.txt;file3.txt") // "file{1;2;3}.txt"

// 列表扩展
String expanded = List_expand("file{1;2;3}.txt") // "file1.txt;file2.txt;file3.txt"
```

---

## 函数参考表

### 基本操作

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `List_getLength` | 获取列表长度 | Variable | `list_in`: 输入列表, `[list_sep]`: 分隔符 |
| `List_addItem` | 添加项目 | String | `list_in`: 输入列表, `new_item`: 新项目, `[list_sep]`: 分隔符 |
| `List_insertItem` | 插入项目 | String | `list_in`: 输入列表, `new_item`: 新项目, `insert_idx`: 插入位置, `[list_sep]`: 分隔符 |
| `List_getItem` | 获取项目 | String | `list_in`: 输入列表, `get_idx`: 索引, `[list_sep]`: 分隔符 |
| `List_removeItem` | 移除项目 | String | `list_in`: 输入列表, `remove_idx`: 索引, `[list_sep]`: 分隔符 |
| `List_removeItemByString` | 按值移除项目 | String | `list_in`: 输入列表, `remove_str`: 移除值, `[list_sep]`: 分隔符 |
| `List_pop` | 弹出最后一个项目 | String | `&list_in`: 输入列表(引用), `[list_sep]`: 分隔符 |

### 列表处理

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `List_convertSeparator` | 转换分隔符 | String | `list_in`: 输入列表, `orig_sep`: 原分隔符, `[new_sep]`: 新分隔符 |
| `List_extend` | 扩展列表 | String | `listA_in`: 列表A, `listB_in`: 列表B |
| `List_unique` | 列表去重 | String | `list_in`: 输入列表, `[list_sep]`: 分隔符 |
| `List_difference` | 列表差集 | String | `listA_in`: 列表A, `listB_in`: 列表B, `[list_sep]`: 分隔符 |
| `List_reverse` | 列表反转 | String | `list_in`: 输入列表, `[list_sep]`: 分隔符 |
| `List_sort` | 列表排序 | String | `list_in`: 输入列表, `[list_sep]`: 分隔符 |
| `List_sortNumeric` | 数字列表排序 | String | `list_in`: 输入列表, `[list_sep]`: 分隔符 |

### 列表查询

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `List_hasItem` | 检查项目是否存在 | Variable | `list_in`: 输入列表, `test_item`: 测试项目, `[list_sep]`: 分隔符 |
| `List_getItemIndex` | 获取项目索引 | Variable | `list_in`: 输入列表, `test_item`: 测试项目, `[list_sep]`: 分隔符 |
| `List_getSlice` | 获取列表切片 | String | `list_in`: 输入列表, `start_idx`: 起始索引, `end_idx`: 结束索引, `[list_sep]`: 分隔符 |

### 高级功能

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `List_compact` | 列表压缩 | String | `list_in`: 输入列表, `[list_sep]`: 分隔符, `[ldelim]`: 左定界符, `[rdelim]`: 右定界符 |
| `List_expand` | 列表扩展 | String | `list_in`: 输入列表, `[list_sep]`: 分隔符, `[ldelim]`: 左定界符, `[rdelim]`: 右定界符 |

---

## 使用示例

### 1. 数据处理流程
```igor
// 处理数据文件列表
Function ProcessFiles(fileList)
    String fileList
    
    // 转换分隔符（如果需要）
    fileList = List_convertSeparator(fileList, ",", new_sep=";")
    
    // 移除无效文件
    fileList = List_removeItemByString(fileList, "invalid.txt")
    
    // 处理每个文件
    Variable count = List_getLength(fileList)
    Variable i
    for (i = 0; i < count; i += 1)
        String fileName = List_getItem(fileList, i)
        LoadAndProcess(fileName)
    endfor
End
```

### 2. 实验参数管理
```igor
// 生成实验参数组合
Function GenerateParameterSets()
    String temperatures = "25;30;35"
    String concentrations = "0.1;0.2;0.3"
    
    // 创建所有组合
    String allSets = ""
    Variable t, c
    for (t = 0; t < List_getLength(temperatures); t += 1)
        for (c = 0; c < List_getLength(concentrations); c += 1)
            String set = List_getItem(temperatures, t) + "C_" + List_getItem(concentrations, c) + "M"
            allSets = List_addItem(allSets, set)
        endfor
    endfor
    
    return allSets
End
```

### 3. 批量文件操作
```igor
// 批量重命名文件
Function RenameFiles(prefix, fileList)
    String prefix, fileList
    
    String newList = ""
    Variable count = List_getLength(fileList)
    Variable i
    for (i = 0; i < count; i += 1)
        String oldName = List_getItem(fileList, i)
        String newName = prefix + oldName
        RenameFile(oldName, newName)
        newList = List_addItem(newList, newName)
    endfor
    
    return newList
End
```

### 4. 数据分析工作流
```igor
// 分析数据集
Function AnalyzeDataSets(dataSets)
    String dataSets
    
    // 处理每个数据集
    while (List_getLength(dataSets) > 0)
        String currentSet = List_pop(dataSets)
        Wave data = $("root:data:" + currentSet)
        
        // 执行分析
        Variable result = AnalyzeWave(data)
        Printf "数据集 %s 结果: %.2f\r", currentSet, result
    endwhile
End
```

### 5. 自动化报告生成
```igor
// 生成报告部分
Function GenerateReportSections()
    // 需要包含的部分
    String requiredSections = "Introduction;Methods;Results;Discussion"
    
    // 已完成的章节
    String completedSections = "Introduction;Results"
    
    // 检查缺少的章节
    String missing = List_difference(requiredSections, completedSections)
    
    if (List_getLength(missing) > 0)
        Print "缺少以下章节:", missing
        return 0
    else
        Print "所有章节已完成"
        return 1
    endif
End
```

### 6. 智能文件列表处理
```igor
// 压缩相似文件名
Function CompressFileList(fileList)
    String fileList
    
    // 压缩前: "data1.txt;data2.txt;data3.txt"
    String compressed = List_compact(fileList, ldelim="[", rdelim="]")
    // 压缩后: "data[1;2;3].txt"
    
    return compressed
End

// 扩展压缩列表
Function ExpandFileList(compressedList)
    String compressedList
    
    // 扩展前: "data[1;2;3].txt"
    String expanded = List_expand(compressedList, ldelim="[", rdelim="]")
    // 扩展后: "data1.txt;data2.txt;data3.txt"
    
    return expanded
End
```

### 7. 实验队列管理
```igor
// 管理实验队列
Function ManageExperimentQueue()
    // 初始队列
    String queue = "exp1;exp2;exp3;exp4"
    
    // 完成第一个实验
    String completed = List_pop(queue)
    Print "已完成:", completed
    Print "剩余队列:", queue
    
    // 添加新实验
    queue = List_insertItem(queue, "exp5", 0)
    Print "更新队列:", queue
    
    // 优先处理紧急实验
    queue = List_insertItem(queue, "urgent", 0)
    Print "最终队列:", queue
End
```

这些函数提供了强大而灵活的列表处理能力，特别适合数据管理、实验参数处理、文件操作和自动化工作流等场景。
