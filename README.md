# Apple 风格 Time Tracking Numbers Template

一个完全数据驱动的 Apple Numbers 时间追踪系统模板。

## 🎯 核心特性

- **完整数据链路**: 数据录入 → 自动计算 → 自动分类 → 自动汇总 → 自动统计 → 自动图表
- **跨午夜支持**: 正确处理 23:00 → 01:00 (2小时) 和 17:00 → 00:00 (7小时) 等情况
- **动态公式**: 所有统计完全由公式驱动，无需手动更新
- **Apple Shortcuts 集成**: 可通过快捷指令自动添加数据

## 📊 包含的 Sheets

### 1. 📋 Settings - 分类管理
- 维护所有时间分类
- 每个分类有对应的颜色代码和图标
- 可自定义扩展新的分类

**默认分类:**
- 📚 Study (紫色)
- 💼 Work (橙色)
- 😴 Sleep (蓝色)
- 🧍 Personal (粉色)
- 🏃 Exercise (绿色)
- 🎮 Entertainment (黄色)
- 📦 Other (灰色)

### 2. ⏰ Time Log - 原始数据表
**核心数据表，所有数据由此开始**

| 列 | 字段 | 类型 | 说明 |
|----|------|------|------|
| A | ID | 计算 | 自动编号 |
| B | Date | 日期 | 记录日期 (格式: yyyy-mm-dd) |
| C | Start Time | 时间 | 开始时间 (格式: hh:mm) |
| D | End Time | 时间 | 结束时间 (格式: hh:mm) |
| E | Duration (min) | 数值 | 持续时间 (分钟) - 自动计算 |
| F | Duration | 文本 | 格式化持续时间 (如: 8h 30m) - 自动计算 |
| G | Category | 文本 | 分类 - 从下拉列表选择 |
| H | Project | 文本 | 项目名称 (可选) |
| I | Note | 文本 | 备注 (可选) |
| J | Month | 数值 | 月份 - 自动计算 |
| K | Year | 数值 | 年份 - 自动计算 |

### 3. 🏠 Overview - 仪表板
**主要视图，展示核心指标**

- 当前月份选择 (B3单元格)
- 总计追踪时间
- 记录数量
- 分类时间分布卡片
  - 每个分类的小时数
  - 百分比

### 4. 📊 Monthly - 月度统计
**详细的月度分析**

- 月份选择区域 (B3单元格) - **修改此处可切换月份**
- 概览统计:
  - 总计追踪时间 (小时)
  - 总记录数
  - 每日平均时间
  - 最活跃的一天
- 分类明细:
  - 每个分类的小时数、分钟数、总分钟数、百分比

### 5. 📅 Daily - 每日统计
**每日时间追踪**

- 显示当月每一天的时间记录
- 每天的总时间
- 每个分类每天的时间分布

### 6. 📈 Trend - 月度趋势
**年度趋势分析**

- 显示2026年每个月的总时间
- 每个月的记录数量

### 7. 📊 Charts Data - 图表数据源
**为图表提供数据源**

- 饼图数据: 分类分布
- 折线图数据: 月度趋势

## 📱 Apple Shortcuts 集成

### 快捷指令写入配置

要通过 Apple Shortcuts 自动添加数据到 Time Log：

**Sheet:** `⏰ Time Log`

**必须字段:**
- B 列: Date (日期, 格式: yyyy-mm-dd)
- C 列: Start Time (开始时间, 格式: hh:mm)
- D 列: End Time (结束时间, 格式: hh:mm)
- G 列: Category (分类, 从下拉列表选择)

**可选字段:**
- H 列: Project (项目名称)
- I 列: Note (备注)

**自动计算字段:**
- A 列: ID (自动编号)
- E 列: Duration (minutes) (自动计算)
- F 列: Duration (formatted) (自动格式化)
- J 列: Month (自动提取)
- K 列: Year (自动提取)

### Shortcut 示例

```
Add Row to Numbers
→ Select Table: ⏰ Time Log
→ Set Date: [当前日期]
→ Set Start Time: [开始时间]
→ Set End Time: [结束时间]
→ Set Category: [分类]
→ Set Project: [项目]
→ Set Note: [备注]
```

## 🎨 设计特点

- **Apple 风格**: 简洁、现代、原生的 macOS/iOS 视觉语言
- **Pastel 颜色**: 低饱和度的柔和颜色
  - 紫色 (Study)
  - 橙色 (Work)
  - 蓝色 (Sleep)
  - 粉色 (Personal)
  - 绿色 (Exercise)
  - 黄色 (Entertainment)
  - 灰色 (Other)
- **无荧光色**: 避免使用高饱和度的颜色
- **无渐变**: 使用纯色填充

## ✅ 验证测试

### Test 1: 新增记录
```
新增: 29 Aug, 09:00-10:00, Study
结果: Study +1h
```

### Test 2: 累加记录
```
新增: 29 Aug, 14:00-15:30, Study
结果: Study 总计 = 2h30m
```

### Test 3: 跨午夜记录
```
新增: 29 Aug, 17:00-00:00, Work
结果: Work +7h (正确处理跨午夜)
```

### Test 4: 切换月份
```
在 Monthly sheet 中修改 B3 为 "July 2026"
结果: 所有统计仅显示7月数据，8月数据隐藏
```

### Test 5: 删除记录
```
删除一条 Study 记录
结果: Study 总时间、百分比、所有相关统计自动减少
```

### Test 6: 修改分类
```
将某条记录的 Category 从 Study 改为 Work
结果: Study 统计减少，Work 统计增加
```

## 💡 使用技巧

1. **切换月份**: 在 `📊 Monthly` sheet 中修改 B3 单元格的值
2. **添加新分类**: 在 `📋 Settings` sheet 中添加新行
3. **自动扩展**: 在 Time Log 中添加新行时，公式会自动扩展
4. **创建图表**: 在 Numbers 中，从 `📊 Charts Data` sheet 创建图表
5. **最佳体验**: 在 Apple Numbers 中打开，所有功能都会正常工作

## 📝 公式说明

### Time Log 中的核心公式

**Duration (min) - E 列:**
```
=IF(OR(C2="",D2=""), "", 
  IF(D2>=C2, 
    (HOUR(D2)-HOUR(C2))*60+(MINUTE(D2)-MINUTE(C2)), 
    (24-HOUR(C2))*60-MINUTE(C2)+HOUR(D2)*60+MINUTE(D2)))
)
```
- 如果结束时间 >= 开始时间: 直接计算差值
- 否则: 计算 (24:00 - 开始时间) + 结束时间

**Duration (formatted) - F 列:**
```
=IF(E2="", "", TEXT(INT(E2/60),"/0")&"h "&TEXT(MOD(E2,60),"/0")&"m")
```
- 将分钟数转换为 "Xh Ym" 格式

### 统计公式

**总计时间:**
```
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!J:J, MONTH(DATEVALUE($B$3)), "⏰ Time Log"!K:K, YEAR(DATEVALUE($B$3)))/60
```
- 按月份和年份过滤，求和 Duration (min)，然后转换为小时

**分类统计:**
```
=SUMIFS("⏰ Time Log"!E:E, "⏰ Time Log"!G:G, "Study", "⏰ Time Log"!J:J, MONTH(DATEVALUE($B$3)), "⏰ Time Log"!K:K, YEAR(DATEVALUE($B$3)))/60
```
- 按分类、月份、年份过滤，求和 Duration (min)，然后转换为小时

## 🔄 更新记录

- 当修改或删除 Time Log 中的数据时，所有统计和图表会自动更新
- 无需手动刷新或重新计算
- Numbers 会自动处理所有公式链

## 📦 文件格式

- **当前格式**: Excel (.xlsx)
- **推荐使用**: Apple Numbers
- **兼容性**: 可在 Excel 中打开，但建议使用 Numbers 以获得最佳体验

## 🎯 重要提醒

1. **不要手动输入统计数字** - 所有数字都由公式自动计算
2. **不要修改公式** - 除非你知道自己在做什么
3. **保持数据结构** - 新增数据时，请遵循现有的列结构
4. **定期备份** - 虽然是模板，但你的数据很重要

---

**创建时间**: 2026-08-29  
**版本**: 1.0  
**作者**: Arena.ai Coding Agent  
**许可**: 可自由使用、修改和分发
