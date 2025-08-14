# StringUtils.ipf 使用文档

## 概述
`StringUtils.ipf` 提供了处理字符串的实用函数，包括字符串验证、正则表达式处理、字符串操作和比较等功能。

---

## 核心功能

### 1. 字符串验证
```igor
// 检查字符串是否为 null
Variable nullCheck = isStringNull(str) 

// 检查字符串是否有效存在
Variable exists = isStringExists(str)

// 检查两个字符串是否相等（区分大小写）
Variable equal = isStringsEqual(str1, str2)

// 检查两个字符串是否相等（不区分大小写）
Variable equalNoCase = isStringsEqual_NoCase(str1, str2)
```

### 2. 字符串操作
```igor
// 为自由名称添加引号
String quoted = String_quoteForLiberalName("my wave")

// 修剪字符串两端空白
String trimmed = String_trim("  hello world  ")

// 反向搜索字符串
Variable lastIndex = String_searchBack("abc_def_ghi", "_") // 返回 7

// 查找共同前缀长度
Variable prefixLen = String_findCommonPrefix("abc123", "abc456") // 返回 3

// 查找共同后缀长度
Variable suffixLen = String_findCommonSuffix("123xyz", "456xyz") // 返回 3
```

### 3. 正则表达式处理
```igor
// 获取第一个正则匹配
String match = String_getRegexMatch("abc123def", "[0-9]+") // 返回 "123"

// 查找所有正则匹配
String matches = String_findRegex("a1 b2 c3", "[a-z]([0-9])") // 返回 "1;2;3"

// 删除正则匹配的部分
String pruned = String_pruneRegex("abc123def", "[0-9]+") // 返回 "abcdef"

// 替换正则匹配的部分
String replaced = String_replaceRegex("abc123def", "[0-9]+", "XYZ") // 返回 "abcXYZdef"
```

---

## 函数参考表

### 字符串验证

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `isStringNull` | 检查字符串是否为 null | Variable | `in_string`: 输入字符串 |
| `isStringExists` | 检查字符串是否有效存在 | Variable | `in_string`: 输入字符串 |
| `isStringsEqual` | 检查字符串相等(区分大小写) | Variable | `stringA`: 字符串A, `stringB`: 字符串B |
| `isStringsEqual_NoCase` | 检查字符串相等(不区分大小写) | Variable | `stringA`: 字符串A, `stringB`: 字符串B |

### 字符串操作

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `String_quoteForLiberalName` | 为自由名称添加引号 | String | `string_in`: 输入字符串 |
| `String_trim` | 修剪字符串两端空白 | String | `string_in`: 输入字符串 |
| `String_searchBack` | 反向搜索字符串 | Variable | `string_in`: 输入字符串, `find_str`: 查找字符串, `[start]`: 起始位置 |
| `String_findCommonPrefix` | 查找共同前缀长度 | Variable | `str1`: 字符串1, `str2`: 字符串2 |
| `String_findCommonSuffix` | 查找共同后缀长度 | Variable | `str1`: 字符串1, `str2`: 字符串2 |

### 正则表达式处理

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `String_getRegexMatch` | 获取第一个正则匹配 | String | `string_in`: 输入字符串, `regex`: 正则表达式 |
| `String_findRegex` | 查找所有正则匹配 | String | `string_in`: 输入字符串, `regex`: 正则表达式 |
| `String_pruneRegex` | 删除正则匹配的部分 | String | `string_in`: 输入字符串, `regex`: 正则表达式 |
| `String_replaceRegex` | 替换正则匹配的部分 | String | `string_in`: 输入字符串, `regex`: 正则表达式, `replace`: 替换字符串 |

---

## 使用示例

### 1. 数据清洗
```igor
// 从混合字符串中提取纯数字
Function ExtractNumbers(input)
    String input
    return String_findRegex(input, "\\d+")
End

// 示例: ExtractNumbers("a1b23c456") 返回 "1;23;456"
```

### 2. 文件名处理
```igor
// 安全创建自由名称
Function CreateSafeWave(name)
    String name
    String safeName = String_quoteForLiberalName(name)
    Make/O $safeName
End
```

### 3. 文本解析
```igor
// 解析日志文件中的时间戳
Function ParseLogTimestamps(log)
    String log
    String timestamps = String_findRegex(log, "\\d{2}:\\d{2}:\\d{2}")
    return timestamps
End
```

### 4. 字符串比较
```igor
// 查找最长的共同前缀
Function/S FindLongestCommonPrefix(strings)
    String strings
    
    Variable count = ItemsInList(strings)
    if (count == 0)
        return ""
    endif
    
    String first = StringFromList(0, strings)
    Variable maxLen = strlen(first)
    
    for (i = 1; i < count; i += 1)
        String current = StringFromList(i, strings)
        maxLen = min(maxLen, String_findCommonPrefix(first, current))
    endfor
    
    return first[0, maxLen-1]
End
```

### 5. 文本格式化
```igor
// 移除字符串中所有空白
Function/S RemoveAllWhitespace(input)
    String input
    return String_pruneRegex(input, "\\s+")
End

// 示例: RemoveAllWhitespace("a b c") 返回 "abc"
```

---

## 正则表达式说明

### `String_getRegexMatch`
- 使用单个正则表达式模式
- 返回第一个匹配的子字符串
- 示例: `String_getRegexMatch("abc123", "\\d+")` → "123"

### `String_findRegex`
- 查找所有匹配项
- 需要包含一个捕获组
- 返回匹配项的列表
- 示例: `String_findRegex("a1 b2 c3", "[a-z](\\d)")` → "1;2;3"

### `String_pruneRegex`
- 删除所有匹配项
- 不能包含捕获组
- 示例: `String_pruneRegex("abc123def", "\\d+")` → "abcdef"

### `String_replaceRegex`
- 替换所有匹配项
- 不能包含捕获组
- 示例: `String_replaceRegex("abc123def", "\\d+", "XYZ")` → "abcXYZdef"

---

## 注意事项

1. **正则表达式语法**：Igor Pro 使用 PCRE 兼容的正则表达式语法
2. **性能考虑**：处理大文本时，正则表达式操作可能较慢
3. **特殊字符处理**：使用双反斜杠转义特殊字符（如 `\\d` 表示数字）
4. **区分大小写**：默认区分大小写，使用 `(?i)` 前缀可忽略大小写
5. **多行处理**：使用 `(?m)` 前缀支持多行匹配

这些函数提供了强大的字符串处理能力，特别适合数据清洗、日志解析和文本处理任务。
