# NumUtils.ipf 使用文档

## 概述
`NumUtils.ipf` 提供了处理数值的实用函数，包括 NaN 检测、复数转换、量级计算和数值比较等功能。

---

## 核心功能

### 1. NaN 检测
```igor
// 检测变量是否为 NaN
Variable result = isNaN(value)
```

### 2. 复数转换
```igor
// 将复数转换为字符串
Variable/C cmplx = cmplx(3, 4)
String str = Num_complexToString(cmplx) // "3 + 4i"
```

### 3. 量级计算
```igor
// 获取数值的单位量级（以3为倍数）
Variable magnitude = Num_getUnitMagnitude(0.00045) // 返回 -6 (微)
magnitude = Num_getUnitMagnitude(2500000)         // 返回 6 (兆)
```

### 4. 数值比较
```igor
// 比较两个数值是否相等（考虑NaN和无穷大）
Variable equal = Nums_areEqual(0.1 + 0.2, 0.3) // 返回 1 (true)

// 使用自定义容差
equal = Nums_areEqual(1.000001, 1.000002, tol=0.00001) // 返回 1
```



## 使用说明

### 1. NaN 检测
`isNaN` 函数是 Igor Pro 内置 `numtype()` 函数的封装，提供更直观的 NaN 检测：
```igor
if (isNaN(myValue))
    Print "值无效"
endif
```

### 2. 复数处理
`Num_complexToString` 将复数转换为标准数学表示：
```igor
Variable/C z = cmplx(1.5, -2.5)
String str = Num_complexToString(z) // "1.5 - 2.5i"
```

### 3. 单位量级计算
`Num_getUnitMagnitude` 返回数值的 SI 单位量级（以3为倍数）：
- 返回 -24 (幺)
- 返回 -21 (仄)
- ...
- 返回 -3 (毫)
- 返回 0 (基本单位)
- 返回 3 (千)
- ...
- 返回 24 (尧)

应用场景：
```igor
Function/S FormatWithSIprefix(value)
    Variable value
    Variable exp = Num_getUnitMagnitude(value)
    Variable scaled = value / (10^exp)
    return num2str(scaled) + GetSIprefix(exp)
End
```

### 4. 数值比较
`Nums_areEqual` 提供智能数值比较：
- 处理 NaN 和 Inf 特殊情况
- 默认使用双精度容差 (1e-13)
- 支持自定义容差

```igor
// 特殊值比较
Nums_areEqual(NaN, NaN)        // 返回 1 (true)
Nums_areEqual(Inf, Inf)        // 返回 1
Nums_areEqual(-Inf, -Inf)      // 返回 1
Nums_areEqual(Inf, -Inf)       // 返回 0

// 浮点数精确比较
Nums_areEqual(0.1 + 0.2, 0.3)  // 返回 1

// 大数比较
Nums_areEqual(1e15, 1e15+1)    // 返回 0
Nums_areEqual(1e15, 1e15+1, tol=2) // 返回 1
```

---

## 常量说明

### `NUM_REGEX`
用于匹配数字的正则表达式模式，可以识别：
- 整数 (123)
- 小数 (12.3, .456)
- 科学计数法 (1.23e-4, 5.6E+7)
- 正负号

### `kTOLd`
双精度数值比较的默认容差 (1e-13)

---

## 应用示例

### 数据验证
```igor
Function ProcessData(data)
    Wave data
    
    // 检查无效数据点
    Variable i
    for (i=0; i<numpnts(data); i+=1)
        if (isNaN(data[i]))
            Print "发现无效数据点：", i
        endif
    endfor
End
```

### 科学数据格式化
```igor
Function/S FormatScientificValue(value)
    Variable value
    
    Variable exp = Num_getUnitMagnitude(value)
    Variable scaled = value / (10^exp)
    
    // 获取SI前缀（需要自定义实现）
    String prefix = GetSIprefix(exp)
    
    return num2str(scaled) + " " + prefix
End
```

### 自动化测试
```igor
Function TestCalculations()
    // 理论值
    Variable expected = 0.3
    
    // 实际计算值
    Variable actual = 0.1 + 0.2
    
    // 验证结果
    if (!Nums_areEqual(actual, expected))
        Abort "计算错误：期望 " + num2str(expected) + "，实际 " + num2str(actual)
    endif
End
```
---

## 函数参考表

| 函数名 | 描述 | 返回值类型 | 参数 |
|--------|------|------------|------|
| `isNaN` | 检测是否为 NaN | Variable | `var`: 输入值 |
| `Num_complexToString` | 复数转字符串 | String | `cmplx_num`: 复数 |
| `Num_getUnitMagnitude` | 获取单位量级 | Variable | `num`: 输入值 |
| `Nums_areEqual` | 比较数值相等性 | Variable | `numA`: 值A, `numB`: 值B, `[tol]`: 容差 |

---
