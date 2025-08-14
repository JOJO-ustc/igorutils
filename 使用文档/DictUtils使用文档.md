# DictUtils.ipf 使用文档

## 概述
`DictUtils.ipf` 提供了操作字典（键值对字符串）的实用函数，支持添加、获取、删除键值对，以及字典转换和查询等功能。字典格式为 `key1:value1;key2:value2;...`。

---

## 核心功能

### 1. 字典操作
```igor
// 添加/更新键值对
String dict = Dict_addItem("", "name", "John") // "name:John;"

// 获取键值
String name = Dict_getItem(dict, "name") // "John"

// 删除键值对
dict = Dict_removeItem(dict, "name") // 空字典

// 检查键是否存在
Variable hasKey = Dict_hasKey(dict, "age") // 0 (false)

// 检查键值对是否存在
Variable hasItem = Dict_hasItem(dict, "name", "John") // 1 (true)
```

### 2. 字典遍历
```igor
// 获取字典长度
Variable length = Dict_getLength(dict) // 键值对数量

// 按索引获取键值对
String pair = Dict_getPairByIndex(dict, 0) // "name:John"

// 按索引获取键
String key = Dict_getKeyByIndex(dict, 0) // "name"

// 获取所有键
String keys = Dict_getKeys(dict) // "name;age;..."
```

### 3. 字典转换
```igor
// 转换键分隔符
String newDict = Dict_convertKeySep(dict, "=", new_sep=":") 
// 将 "name=John;age=30" 转换为 "name:John;age:30"
```

---

## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| **基本操作** |  |  |  |
| `Dict_addItem` | 添加/更新键值对 | String | `dict`: 字典, `key`: 键, `value`: 值 |
| `Dict_getItem` | 获取键对应的值 | String | `dict`: 字典, `key`: 键 |
| `Dict_removeItem` | 删除键值对 | String | `dict`: 字典, `key`: 键 |
| `Dict_hasKey` | 检查键是否存在 | Variable | `dict`: 字典, `key`: 键 |
| `Dict_hasItem` | 检查键值对是否存在 | Variable | `dict`: 字典, `key`: 键, `value`: 值 |
| **字典遍历** |  |  |  |
| `Dict_getLength` | 获取键值对数量 | Variable | `dict`: 字典 |
| `Dict_getPairByIndex` | 按索引获取键值对 | String | `dict`: 字典, `index`: 索引 |
| `Dict_getKeyByIndex` | 按索引获取键 | String | `dict`: 字典, `index`: 索引, `[key_sep]`: 键分隔符, `[list_sep]`: 列表分隔符 |
| `Dict_getKeys` | 获取所有键 | String | `dict`: 字典 |
| **字典转换** |  |  |  |
| `Dict_convertKeySep` | 转换键分隔符 | String | `dict`: 字典, `old_sep`: 原分隔符, `[new_sep]`: 新分隔符, `[list_sep]`: 列表分隔符 |

---

## 使用示例

### 1. 创建和操作字典
```igor
// 创建空字典
String dict = ""

// 添加键值对
dict = Dict_addItem(dict, "name", "Alice")
dict = Dict_addItem(dict, "age", "30")
dict = Dict_addItem(dict, "occupation", "Engineer")

// 获取值
Print Dict_getItem(dict, "name") // 输出 "Alice"

// 更新值
dict = Dict_addItem(dict, "age", "31")

// 删除键
dict = Dict_removeItem(dict, "occupation")
```

### 2. 遍历字典
```igor
Function PrintDictionary(dict)
    String dict
    
    Variable count = Dict_getLength(dict)
    Print "字典内容 (" + num2str(count) + " 项):"
    
    Variable i
    for (i = 0; i < count; i += 1)
        String key = Dict_getKeyByIndex(dict, i)
        String value = Dict_getItem(dict, key)
        Printf "  %s: %s\r", key, value
    endfor
End
```

### 3. 解析系统信息
```igor
Function/S GetSystemInfo()
    String info = ""
    
    // 添加系统信息
    info = Dict_addItem(info, "OS", IgorInfo(2))
    info = Dict_addItem(info, "IgorVersion", IgorInfo(1))
    info = Dict_addItem(info, "FreeMemory", num2str(IgorInfo(0)))
    
    return info
End

// 使用示例
String sysInfo = GetSystemInfo()
Print "操作系统:", Dict_getItem(sysInfo, "OS")
Print "Igor版本:", Dict_getItem(sysInfo, "IgorVersion")
```

### 4. 配置管理
```igor
Function LoadConfig()
    // 从文件加载配置
    String config = "timeout=500;retries=3;mode=advanced"
    
    // 转换为标准字典格式
    config = Dict_convertKeySep(config, "=", new_sep=":")
    
    // 获取配置值
    Variable timeout = str2num(Dict_getItem(config, "timeout"))
    Variable retries = str2num(Dict_getItem(config, "retries"))
    String mode = Dict_getItem(config, "mode")
    
    // 应用配置...
End
```

### 5. 函数参数解析
```igor
Function ExecuteCommand(params)
    String params // "action=run;target=data1;options=verbose"
    
    // 转换为字典
    params = Dict_convertKeySep(params, "=", new_sep=":")
    
    String action = Dict_getItem(params, "action")
    String target = Dict_getItem(params, "target")
    String options = Dict_getItem(params, "options")
    
    if (cmpstr(action, "run") == 0)
        Print "运行分析:", target
        if (stringmatch(options, "*verbose*"))
            Print "详细模式已启用"
        endif
    endif
End
```

---

## 常量说明

### `KEYSEP`
默认键值分隔符为冒号 (`:`)

### `LISTSEP` (来自 listutils)
默认键值对分隔符为分号 (`;`)

---

## 高级用法

### 嵌套字典
```igor
Function/S CreateUserProfile()
    String profile = ""
    
    // 基本信息
    profile = Dict_addItem(profile, "name", "Bob")
    profile = Dict_addItem(profile, "email", "bob@example.com")
    
    // 地址信息（子字典）
    String address = ""
    address = Dict_addItem(address, "street", "123 Main St")
    address = Dict_addItem(address, "city", "Springfield")
    address = Dict_addItem(address, "zip", "12345")
    profile = Dict_addItem(profile, "address", address)
    
    return profile
End

// 解析嵌套字典
Function PrintAddress(profile)
    String profile
    
    String address = Dict_getItem(profile, "address")
    String street = Dict_getItem(address, "street")
    String city = Dict_getItem(address, "city")
    
    Printf "地址: %s, %s\r", street, city
End
```

### 字典合并
```igor
Function/S MergeDictionaries(dict1, dict2)
    String dict1, dict2
    
    String result = dict1
    
    // 获取dict2的所有键
    String keys = Dict_getKeys(dict2)
    Variable count = ItemsInList(keys)
    Variable i
    
    for (i = 0; i < count; i += 1)
        String key = StringFromList(i, keys)
        String value = Dict_getItem(dict2, key)
        result = Dict_addItem(result, key, value)
    endfor
    
    return result
End
```

### 字典过滤
```igor
Function/S FilterDictionary(dict, keyPattern)
    String dict, keyPattern
    
    String result = ""
    String keys = Dict_getKeys(dict)
    Variable count = ItemsInList(keys)
    Variable i
    
    for (i = 0; i < count; i += 1)
        String key = StringFromList(i, keys)
        if (stringmatch(key, keyPattern))
            String value = Dict_getItem(dict, key)
            result = Dict_addItem(result, key, value)
        endif
    endfor
    
    return result
End

// 使用示例：只保留以 "temp" 开头的键
String filtered = FilterDictionary(sensorData, "temp*")
```

这些函数提供了一种轻量级但强大的方式来处理键值对数据，特别适合配置管理、数据解析和系统信息收集等场景。
