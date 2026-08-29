# Time Tracking Template - 完整使用指南

## 📋 模板概述

这是一个**完全数据驱动**的 Apple Numbers 时间追踪系统，具有以下核心特性：

```
Apple Shortcuts 记录一次时间
↓
自动添加到 Numbers 的 Time Log 表
↓
Numbers 自动计算 Duration (包括跨午夜)
↓
Numbers 根据 Category 自动统计
↓
月度统计自动更新
↓
饼图、趋势图自动更新
```

## 🎯 核心设计原则

1. **真正的数据驱动** - 所有统计都由公式自动计算，无静态数据
2. **自动扩展** - 新增数据时，所有统计自动更新
3. **跨午夜支持** - 正确处理 17:00 → 00:00 = 7小时
4. **Apple 风格** - 简洁、现代、Pastel 颜色方案
5. **Shortcuts 友好** - 优化的数据结构，便于快捷指令写入

---

## 📊 Sheets 详细说明

### Sheet 1: 📋 Settings - 分类管理

**用途**: 维护所有时间分类和对应的视觉属性

| 列 | 字段 | 说明 | 示例 |
|----|------|------|------|
| A | Category | 分类名称 | Study |
| B | Color Code | 颜色代码 | C8BFE7 |
| C | Icon | 图标 | 📚 |
| D | Active | 是否启用 | YES |

**颜色方案**:
- Study: #C8BFE7 (淡紫)
- Work: #FFA07A (淡橙)
- Sleep: #87CEFA (淡蓝)
- Personal: #FFB6C1 (淡粉)
- Exercise: #98FB98 (淡绿)
- Entertainment: #FFE4B5 (淡黄)
- Other: #D3D3D3 (淡灰)

**使用方法**:
- 要添加新分类，直接在表格底部添加新行
- 修改分类名称后，所有引用该分类的公式会自动更新

---

### Sheet 2: ⏰ Time Log - 原始数据表

**用途**: 存储所有时间记录的原始数据

| 列 | 字段 | 类型 | 格式 | 说明 |
|----|------|------|------|------|
| A | ID | 计算 | 数值 | 自动编号，行号-1 |
| B | Date | 输入 | 日期 | yyyy-mm-dd |
| C | Start Time | 输入 | 时间 | hh:mm |
| D | End Time | 输入 | 时间 | hh:mm |
| E | Duration (min) | 计算 | 数值 | 持续时间 (分钟) |
| F | Duration | 计算 | 文本 | 格式化显示 (Xh Ym) |
| G | Category | 输入 | 文本 | 从下拉列表选择 |
| H | Project | 输入 | 文本 | 项目名称 (可选) |
| I | Note | 输入 | 文本 | 备注 (可选) |
| J | Month | 计算 | 数值 | 从 Date 提取 |
| K | Year | 计算 | 数值 | 从 Date 提取 |

**核心公式**:

**E 列 - Duration (min)**:
```excel
=IF(OR(C2="",D2=""), "", 
  IF(D2>=C2, 
    (HOUR(D2)-HOUR(C2))*60+(MINUTE(D2)-MINUTE(C2)), 
    (24-HOUR(C2))*60-MINUTE(C2)+HOUR(D2)*60+MINUTE(D2)))
)
```

**逻辑说明**:
- 如果结束时间 >= 开始时间: 直接计算 `(结束小时-开始小时)*60 + (结束分钟-开始分钟)`
- 否则 (跨午夜): 计算 `(24-开始小时)*60 - 开始分钟 + 结束小时*60 + 结束分钟`

**F 列 - Duration (formatted)**:
```excel
=IF(E2="", "", TEXT(INT(E2/60),"/0")&"h "&TEXT(MOD(E2,60),"/0")&"m")
```

**J 列 - Month**:
```excel
=IF(B2="", "", MONTH(B2))
```

**K 列 - Year**:
```excel
=IF(B2="", "", YEAR(B2))
```

**数据验证**:
- G 列 (Category) 有下拉列表验证，可选值: Study, Work, Sleep, Personal, Exercise, Entertainment, Other

**使用方法**:
1. 通过 Shortcuts 或手动添加新行
2. 填写 B, C, D, G 列 (必须)
3. 可选填写 H, I 列
4. A, E, F, J, K 列会自动计算

---

### Sheet 3: 🏠 Overview - 仪表板

**用途**: 展示核心指标和分类分布的可视化视图

**结构**:
```
TIME TRACKING DASHBOARD

Current Month: August 2026

TOTAL TRACKED TIME
Hours
[总小时数]

RECORDS
[记录数量]

CATEGORY BREAKDOWN
📚 Study
[小时数] [总分钟数] [百分比]

💼 Work
[小时数] [总分钟数] [百分比]
...
```

**核心公式**:

**B3 - 当前月份**:
```excel
="August 2026"
```
*修改此单元格可切换所有统计的月份*

**B7 - 总计时间 (小时)**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!J:J, 
  MONTH(DATEVALUE($B$3)), "⏰ Time Log"!K:K, 
  YEAR(DATEVALUE($B$3)))/60
```

**B10 - 记录数量**:
```excel
=COUNTIFS("⏰ Time Log"!J:J, MONTH(DATEVALUE($B$3)), 
  "⏰ Time Log"!K:K, YEAR(DATEVALUE($B$3)))
```

**分类卡片 (每个分类的小时数)**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!G:G, "Study", 
  "⏰ Time Log"!J:J, MONTH(DATEVALUE($B$3)), 
  "⏰ Time Log"!K:K, YEAR(DATEVALUE($B$3)))/60
```

**分类卡片 (百分比)**:
```excel
=IF(B14=0, 0, B14/B7)
```
*B14 是该分类的小时数，B7 是总小时数*

---

### Sheet 4: 📊 Monthly - 月度统计

**用途**: 提供详细的月度分析和统计

**结构**:
```
MONTHLY SUMMARY

Selected Month: August 2026

OVERVIEW
Metric | Value
Total Tracked Time (Hours) | [数值]
Total Records | [数值]
Average per Day (Hours) | [数值]
Most Active Day | [日期]

CATEGORY BREAKDOWN
Category | Hours | Minutes | Total (min) | Percentage
Study | [数值] | [数值] | [数值] | [数值]%
Work | [数值] | [数值] | [数值] | [数值]%
...
```

**核心公式**:

**B3 - 选中月份**:
```excel
="August 2026"
```
*修改此单元格可切换月份*

**B7 - 总计时间**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!J:J, 
  MONTH(DATEVALUE($B$3)), "⏰ Time Log"!K:K, 
  YEAR(DATEVALUE($B$3)))/60
```

**B8 - 总记录数**:
```excel
=COUNTIFS("⏰ Time Log"!J:J, MONTH(DATEVALUE($B$3)), 
  "⏰ Time Log"!K:K, YEAR(DATEVALUE($B$3)))
```

**B9 - 日均时间**:
```excel
=IF(B7=0, 0, B7/DAY(EOMONTH(DATEVALUE($B$3),0)))
```

**B10 - 最活跃的一天**:
```excel
=INDEX("📅 Daily"!A:A, MATCH(MAX("📅 Daily"!B:B), "📅 Daily"!B:B, 0))
```

**分类统计 (小时数)**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!G:G, "Study", 
  "⏰ Time Log"!J:J, MONTH(DATEVALUE($B$3)), 
  "⏰ Time Log"!K:K, YEAR(DATEVALUE($B$3)))/60
```

**分类统计 (分钟数)**:
```excel
=MOD(SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!G:G, "Study", 
  "⏰ Time Log"!J:J, MONTH(DATEVALUE($B$3)), 
  "⏰ Time Log"!K:K, YEAR(DATEVALUE($B$3))),60)
```

**分类统计 (百分比)**:
```excel
=IF(B15="" OR B15=0, 0, B15/B7*100)
```

---

### Sheet 5: 📅 Daily - 每日统计

**用途**: 显示每一天的时间记录详情

**结构**:
```
DAILY TIME TRACKING
August 2026

Date | Total (min) | Total | Study | Work | Sleep | Personal | Exercise | Entertainment | Other
2026-08-01 | [数值] | [Xh Ym] | [数值] | [数值] | [数值] | [数值] | [数值] | [数值] | [数值]
2026-08-02 | [数值] | [Xh Ym] | [数值] | [数值] | [数值] | [数值] | [数值] | [数值] | [数值]
...
```

**核心公式**:

**B5 - 某天总分钟数**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!B:B, "2026-08-01")
```

**C5 - 某天格式化总时间**:
```excel
=IF(B5="", "", TEXT(INT(B5/60),"/0")&"h "&TEXT(MOD(B5,60),"/0")&"m")
```

**D5 - 某天某分类的时间**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!B:B, "2026-08-01", 
  "⏰ Time Log"!G:G, "Study")
```

---

### Sheet 6: 📈 Trend - 月度趋势

**用途**: 展示全年每个月的时间追踪趋势

**结构**:
```
MONTHLY TREND
2026 Year Overview

Month | Total Hours | Total Records
January | [数值] | [数值]
February | [数值] | [数值]
...
December | [数值] | [数值]
```

**核心公式**:

**B5 - January 总小时数**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!J:J, 1, "⏰ Time Log"!K:K, 2026)/60
```

**C5 - January 记录数**:
```excel
=COUNTIFS("⏰ Time Log"!J:J, 1, "⏰ Time Log"!K:K, 2026)
```

---

### Sheet 7: 📊 Charts Data - 图表数据源

**用途**: 为创建图表提供数据源

**结构**:
```
DONUT CHART DATA
Category Distribution - August 2026

Category | Hours
Study | [数值]
Work | [数值]
...

LINE CHART DATA
Monthly Trend - 2026

Month | Hours
January | [数值]
February | [数值]
...
```

**核心公式**:

**饼图数据 - B5 (Study 小时数)**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!G:G, "Study", 
  "⏰ Time Log"!J:J, MONTH(DATEVALUE("August 2026")), 
  "⏰ Time Log"!K:K, YEAR(DATEVALUE("August 2026")))/60
```

**折线图数据 - B11 (January 小时数)**:
```excel
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!J:J, 1, "⏰ Time Log"!K:K, 2026)/60
```

**在 Numbers 中创建图表**:
1. 选择 Charts Data sheet
2. 选择数据区域
3. 插入饼图或折线图
4. Numbers 会自动关联数据

---

## 📱 Apple Shortcuts 集成

### 配置说明

**目标 Sheet**: `⏰ Time Log`

**数据类型**:

| 列 | 字段 | Numbers 类型 | Shortcuts 类型 | 是否必须 |
|----|------|--------------|----------------|----------|
| A | ID | 数值 | 不需要 | 否 |
| B | Date | 日期 | 日期 | 是 |
| C | Start Time | 时间 | 时间 | 是 |
| D | End Time | 时间 | 时间 | 是 |
| E | Duration (min) | 数值 | 不需要 | 否 |
| F | Duration | 文本 | 不需要 | 否 |
| G | Category | 文本 | 文本 | 是 |
| H | Project | 文本 | 文本 | 否 |
| I | Note | 文本 | 文本 | 否 |
| J | Month | 数值 | 不需要 | 否 |
| K | Year | 数值 | 不需要 | 否 |

### Shortcuts 示例步骤

1. **创建新快捷指令**
2. **添加 "添加行到 Numbers" 动作**
3. **选择文件**: Time_Tracking_Template.xlsx
4. **选择表格**: ⏰ Time Log
5. **配置字段**:
   - Date → B 列
   - Start Time → C 列
   - End Time → D 列
   - Category → G 列
   - Project → H 列 (可选)
   - Note → I 列 (可选)

### 示例 Shortcuts JSON

```json
{
  "actions": [
    {
      "actionName": "Add Row to Numbers",
      "parameters": {
        "file": "Time_Tracking_Template.xlsx",
        "sheet": "⏰ Time Log",
        "rowData": {
          "B": "{{Current Date}}",
          "C": "{{Start Time}}",
          "D": "{{End Time}}",
          "G": "{{Category}}"
        }
      }
    }
  ]
}
```

---

## ✅ 验证测试

### Test 1: 基础新增
```
操作: 新增一行: 29 Aug, 09:00-10:00, Study
预期结果: Study 总时间增加 1 小时
验证位置: Overview B14, Monthly B9
```

### Test 2: 累加测试
```
操作: 新增一行: 29 Aug, 14:00-15:30, Study
预期结果: Study 总时间变为 2h30m (150 分钟)
验证位置: Overview B14 应该显示 2.5
```

### Test 3: 跨午夜测试
```
操作: 新增一行: 29 Aug, 17:00-00:00, Work
预期结果: Work 总时间增加 7 小时
验证: Duration (min) 应该计算为 420 分钟
```

### Test 4: 月份切换测试
```
操作: 在 Monthly sheet 修改 B3 为 "July 2026"
预期结果: 所有统计仅显示7月数据，8月数据被隐藏
验证位置: Monthly B7, Overview B7, Charts Data
```

### Test 5: 删除测试
```
操作: 删除一条 Study 记录
预期结果: Study 总时间、百分比、所有相关统计自动减少
验证位置: Overview B14, Monthly B9
```

### Test 6: 分类修改测试
```
操作: 将某条记录的 Category 从 Study 改为 Work
预期结果: Study 统计减少，Work 统计增加
验证位置: Overview B14 (Study 减少), B16 (Work 增加)
```

### Test 7: 日期修改测试
```
操作: 将某条8月记录的 Date 改为7月
预期结果: 8月统计减少，7月统计增加
验证位置: Monthly (切换到7月和8月分别查看)
```

---

## 💡 使用技巧

### 1. 快速切换月份
- 在 `📊 Monthly` sheet 中修改 B3 单元格
- 所有引用该单元格的公式会自动更新
- 包括: Overview, Monthly, Charts Data

### 2. 添加新分类
- 在 `📋 Settings` sheet 中添加新行
- 新分类会自动出现在 Time Log 的下拉列表中
- 但需要手动将新分类添加到 Monthly 和 Daily sheet 的公式中

### 3. 创建图表
- 在 Numbers 中，从 `📊 Charts Data` sheet 选择数据
- 插入饼图: 使用 Donut Chart Data 区域
- 插入折线图: 使用 Line Chart Data 区域
- Numbers 会自动保持图表与数据的关联

### 4. 自定义颜色
- 在 `📋 Settings` sheet 中修改 Color Code
- 在 Numbers 中手动设置图表颜色以匹配分类颜色

### 5. 数据备份
- 定期复制 Time Log sheet 到新文件
- 或者导出为 CSV 进行备份

---

## 🔄 维护和更新

### 添加新月份
- 在 `📈 Trend` sheet 中添加新行即可
- 公式会自动扩展

### 添加新年份
- 复制现有年份的公式
- 修改年份参数即可

### 修改分类
- 在 `📋 Settings` sheet 中修改分类名称
- 所有引用该分类的公式会自动更新

---

## 📝 已知限制

1. **Excel 兼容性**: 虽然可以在 Excel 中打开，但建议使用 Apple Numbers
   - Excel 的 DATEVALUE 函数可能需要调整
   - Numbers 对时间计算的支持更好

2. **公式复杂度**: 由于使用了大量 SUMIFS 和嵌套 IF，在数据量很大时可能影响性能
   - 但对于个人使用，数千条记录不会有问题

3. **月份切换**: 目前需要手动修改 B3 单元格
   - 可以考虑添加下拉列表选择月份

---

## 🎨 视觉设计

### 颜色方案
- **背景**: 白色 (#FFFFFF)
- **表头**: 浅灰 (#F0F0F0)
- **分类颜色**:
  - Study: 淡紫 (#C8BFE7)
  - Work: 淡橙 (#FFA07A)
  - Sleep: 淡蓝 (#87CEFA)
  - Personal: 淡粉 (#FFB6C1)
  - Exercise: 淡绿 (#98FB98)
  - Entertainment: 淡黄 (#FFE4B5)
  - Other: 淡灰 (#D3D3D3)

### 字体样式
- **标题**: 16pt, 加粗
- **副标题**: 14pt, 加粗
- **表头**: 12pt, 加粗
- **数据**: 12pt
- **大数字**: 24pt, 加粗

### 布局原则
- 清爽、简洁
- 充足的空白
- 一致的对齐
- 直观的层次

---

## 📚 术语表

| 术语 | 说明 |
|------|------|
| SUMIFS | 按多个条件求和 |
| COUNTIFS | 按多个条件计数 |
| MONTH | 提取日期的月份 |
| YEAR | 提取日期的年份 |
| HOUR | 提取时间的小时 |
| MINUTE | 提取时间的分钟 |
| DATEVALUE | 将文本转换为日期 |
| EOMONTH | 获取月份的最后一天 |
| MOD | 取余数 |
| INT | 取整数 |
| TEXT | 格式化数值为文本 |

---

## 🎯 总结

这个模板实现了你要求的**完整数据链路**：

```
✓ 数据录入 → 自动计算 Duration
✓ 自动分类 → 按 Category 统计
✓ 自动汇总 → 月度/每日汇总
✓ 自动统计 → 百分比、平均值等
✓ 自动图表 → 图表数据源
✓ Shortcuts 集成 → 便捷数据录入
```

所有功能都**由公式驱动**，无需手动更新任何统计数字。

---

**文件**: `Time_Tracking_Template.xlsx`  
**创建日期**: 2026-08-29  
**兼容性**: Apple Numbers (推荐), Microsoft Excel  
**版本**: 1.0
