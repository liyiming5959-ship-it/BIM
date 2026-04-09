# 深圳市BIM数据与能力中台 - 总体框架使用说明

## 一、框架概述

本框架为"深圳市BIM数据与能力中台"的总体平台原型框架，采用模块化、组件化、配置化的设计理念，确保后续功能页面的高效接入和持续演进。

### 核心设计原则
- **可扩展性**：菜单、页面、功能均可动态配置和扩展
- **可维护性**：结构清晰，职责分明，便于长期维护
- **统一性**：统一的视觉风格、交互规范、布局结构
- **兼容性**：支持多种页面来源（图片转网页、AI生成、外部接入）

---

## 二、框架结构说明

### 1. 整体布局架构

```
┌─────────────────────────────────────────────────────────────┐
│  顶部栏 (64px)                                               │
│  ├── Logo + 平台名称                                         │
│  ├── 全局搜索框                                              │
│  ├── 消息通知、帮助、用户菜单                                 │
├─────────────────────────────────────────────────────────────┤
│  一级菜单TAB区 (48px)                                        │
│  ├── 平台首页 | 数据中台 | 能力中台 | 共享中心 | 资源中心      │
├──────────┬──────────────────────────────────────────────────┤
│          │  面包屑区 (40px)                                  │
│          │  首页 > 数据中台 > 数据接入 > ...                 │
│          ├──────────────────────────────────────────────────┤
│          │  页面标题区 (56px)                                │
│          │  页面标题 [状态标签] [操作按钮]                    │
│          ├──────────────────────────────────────────────────┤
│  左侧    │                                                  │
│  二级    │  页面内容区 (动态承载区)                          │
│  导航    │                                                  │
│ (240px)  │  ┌────────────────────────────────────────────┐ │
│          │  │                                            │ │
│          │  │        各类业务页面内容区域                 │ │
│          │  │        (通过配置动态加载)                   │ │
│          │  │                                            │ │
│          │  └────────────────────────────────────────────┘ │
└──────────┴──────────────────────────────────────────────────┘
```

### 2. 各区域职责

| 区域 | 高度 | 职责说明 | 设计要点 |
|------|------|----------|----------|
| **顶部栏** | 64px | 平台标识、全局搜索、用户操作 | 深蓝渐变背景，固定定位 |
| **一级菜单TAB** | 48px | 五大核心模块切换 | 白色背景，下划线选中态 |
| **左侧导航** | 自适应 | 二级及以下菜单展示 | 可折叠，支持多级展开 |
| **面包屑** | 40px | 页面层级导航 | 可点击返回上级 |
| **页面标题** | 56px | 标题、状态、操作按钮 | 左标题右操作布局 |
| **内容区** | 自适应 | 动态页面承载 | 支持各类页面接入 |

---

## 三、菜单配置说明

### 1. 菜单配置文件

菜单结构在 `menuConfig` 对象中配置，位于 HTML 文件的 `<script>` 标签内：

```javascript
const menuConfig = {
    home: {
        title: '平台首页',
        icon: 'fa-home',
        items: [
            { id: 'overview', title: '平台概览', icon: 'fa-dashboard' },
            { id: 'workbench', title: '我的工作台', icon: 'fa-desktop' }
        ]
    },
    data: {
        title: '数据中台',
        icon: 'fa-database',
        items: [
            { 
                id: 'access', 
                title: '数据接入', 
                icon: 'fa-upload',
                children: [
                    { id: 'access-config', title: '接入配置' },
                    { id: 'access-task', title: '接入任务' }
                ]
            }
        ]
    }
    // ... 其他模块
};
```

### 2. 菜单配置规范

#### 一级菜单配置（顶部TAB）
```javascript
moduleKey: {
    title: '菜单名称',      // 显示名称
    icon: 'fa-icon',        // Font Awesome图标
    items: []               // 二级菜单数组
}
```

#### 二级菜单配置（左侧导航）
```javascript
{
    id: 'unique-id',        // 唯一标识
    title: '菜单名称',      // 显示名称
    icon: 'fa-icon',        // 图标
    children: []            // 三级菜单（可选）
}
```

#### 三级菜单配置
```javascript
{
    id: 'unique-id',        // 唯一标识
    title: '菜单名称'       // 显示名称
}
```

### 3. 菜单调整方法

**添加新菜单项：**
1. 在对应模块的 `items` 数组中添加新对象
2. 确保 `id` 唯一
3. 选择合适的 `icon`（Font Awesome图标类名）

**调整菜单顺序：**
直接在 `items` 数组中调整对象顺序即可

**添加三级菜单：**
在二级菜单对象中添加 `children` 数组

**删除菜单项：**
直接从 `items` 数组中移除对应对象

---

## 四、页面接入规范

### 1. 页面接入方式

框架支持三种页面接入方式：

#### 方式一：配置化路由（推荐）
在 `menuConfig` 中配置页面路由，框架自动处理：

```javascript
{
    id: 'page-id',
    title: '页面标题',
    icon: 'fa-icon',
    route: '/path/to/page.html'  // 页面路径
}
```

#### 方式二：动态内容加载
通过 JavaScript 动态加载页面内容到内容区：

```javascript
function loadPage(pageId) {
    const contentArea = document.getElementById('contentArea');
    // 根据 pageId 加载对应页面内容
    contentArea.innerHTML = getPageContent(pageId);
}
```

#### 方式三：iframe嵌入
对于外部页面或第三方系统，使用iframe嵌入：

```javascript
{
    id: 'external-page',
    title: '外部页面',
    type: 'iframe',
    src: 'https://external-system.com/page'
}
```

### 2. 页面内容规范

接入的页面内容应遵循以下规范：

**布局规范：**
- 使用框架提供的 `.dashboard-grid` 网格系统
- 采用 `.widget` 组件包裹内容块
- 遵循 12 列网格布局（`.widget-col-3` 到 `.widget-col-12`）

**样式规范：**
- 使用框架定义的 CSS 变量（`--primary-color` 等）
- 遵循政务蓝白灰配色体系
- 使用框架提供的组件类（`.btn`, `.status-tag`, `.stat-card` 等）

**交互规范：**
- 按钮使用 `.btn-primary` 或 `.btn-secondary`
- 状态标签使用 `.status-tag.success/warning/info`
- 表格使用 `.data-table` 类

### 3. 页面组件示例

#### 统计卡片
```html
<div class="stat-card">
    <div class="stat-icon blue">
        <i class="fa fa-database"></i>
    </div>
    <div class="stat-info">
        <div class="stat-value">12,580</div>
        <div class="stat-label">数据资源总量</div>
        <div class="stat-change up">+12.5%</div>
    </div>
</div>
```

#### 内容面板
```html
<div class="widget">
    <div class="widget-header">
        <span class="widget-title">面板标题</span>
        <div class="widget-actions">
            <div class="widget-action"><i class="fa fa-cog"></i></div>
        </div>
    </div>
    <div class="widget-body">
        <!-- 页面内容 -->
    </div>
</div>
```

#### 数据表格
```html
<table class="data-table">
    <thead>
        <tr>
            <th>列标题</th>
            <th>列标题</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>数据</td>
            <td>数据</td>
        </tr>
    </tbody>
</table>
```

---

## 五、扩展开发指南

### 1. 添加新的功能模块

**步骤1：添加一级菜单**
在 `menuConfig` 中添加新模块：

```javascript
const menuConfig = {
    // ... 现有模块
    
    newmodule: {
        title: '新模块名称',
        icon: 'fa-new-icon',
        items: [
            { id: 'menu1', title: '菜单1', icon: 'fa-icon1' },
            { id: 'menu2', title: '菜单2', icon: 'fa-icon2' }
        ]
    }
};
```

**步骤2：添加顶部TAB**
在 HTML 的导航区添加：

```html
<div class="nav-tab" data-module="newmodule">
    <i class="fa fa-new-icon" style="margin-right: 6px;"></i>
    新模块名称
</div>
```

**步骤3：创建页面内容**
在 `contentArea` 中添加对应的页面内容，或通过动态加载方式实现。

### 2. 添加页面交互功能

在 `<script>` 标签中添加页面特定的 JavaScript：

```javascript
// 页面初始化
document.addEventListener('DOMContentLoaded', function() {
    // 页面特定逻辑
});

// 菜单点击处理
function handleMenuClick(menuId, hasChildren, element) {
    // 自定义处理逻辑
    console.log('Menu clicked:', menuId);
}
```

### 3. 自定义样式扩展

在 `<style>` 标签中添加自定义样式：

```css
/* 自定义组件样式 */
.custom-component {
    /* 样式定义 */
}

/* 页面特定样式 */
.page-specific .widget {
    /* 特定页面样式 */
}
```

---

## 六、UI设计规范

### 1. 色彩体系

**主色调：**
- 主色：`#0052D9`（政务蓝）
- 主色浅：`#E6F0FF`
- 主色深：`#1E3A8A`

**辅助色：**
- 成功：`#059669`（绿色）
- 警告：`#D97706`（橙色）
- 错误：`#DC2626`（红色）
- 信息：`#2563EB`（蓝色）

**中性色：**
- 标题：`#1F2937`
- 正文：`#4B5563`
- 次要：`#6B7280`
- 边框：`#E5E7EB`
- 背景：`#F3F4F6`

### 2. 字体规范

- 主字体：PingFang SC, Microsoft YaHei, sans-serif
- 标题：18px, font-weight: 600
- 正文：14px, font-weight: 400
- 辅助：13px, font-weight: 400
- 标签：12px, font-weight: 500

### 3. 间距规范

- 大间距：24px
- 中间距：16px
- 小间距：12px
- 微间距：8px

### 4. 圆角规范

- 大圆角：12px（卡片、面板）
- 中圆角：8px（按钮、输入框）
- 小圆角：4px（标签、徽章）
- 全圆角：50%（头像、图标按钮）

---

## 七、响应式适配

### 断点设置

- 大屏：≥1280px（默认布局）
- 中屏：768px - 1279px（调整网格）
- 小屏：<768px（移动端适配）

### 适配规则

**大屏（≥1280px）：**
- 左侧导航 240px
- 内容区自适应
- 4列统计卡片

**中屏（768px-1279px）：**
- 左侧导航可折叠
- 2列统计卡片
- 调整部分组件布局

**小屏（<768px）：**
- 左侧导航隐藏（可滑出）
- 单列布局
- 简化部分功能

---

## 八、最佳实践

### 1. 菜单设计原则

- **层级控制**：建议不超过3级，保持导航清晰
- **命名规范**：使用简洁、明确的菜单名称
- **图标选择**：使用语义化图标，增强识别性
- **分组逻辑**：按功能模块合理分组

### 2. 页面设计原则

- **信息层次**：重要信息突出显示
- **留白合理**：避免内容过于拥挤
- **操作明确**：按钮、链接状态清晰
- **反馈及时**：操作后给予明确反馈

### 3. 性能优化建议

- 图片懒加载
- 组件按需加载
- 避免过多动画
- 优化大数据量表格

### 4. 可访问性

- 支持键盘导航
- 提供文字说明
- 保持色彩对比度
- 支持屏幕阅读器

---

## 九、常见问题

### Q1: 如何修改主题颜色？
在 CSS 变量中修改：
```css
:root {
    --primary-color: #新颜色;
}
```

### Q2: 如何添加新的图标？
使用 Font Awesome 图标库，在菜单配置中设置 `icon: 'fa-icon-name'`。

### Q3: 如何实现页面间的数据传递？
使用 URL 参数、LocalStorage 或全局状态管理。

### Q4: 如何接入外部系统页面？
使用 iframe 方式嵌入，或通过 API 集成数据后在本系统展示。

### Q5: 如何添加页面权限控制？
在菜单配置中添加 `permission` 字段，在渲染时根据用户权限过滤。

---

## 十、后续演进建议

### 1. 功能扩展方向

- **权限管理**：细化到页面、按钮级别的权限控制
- **主题切换**：支持浅色/深色主题切换
- **国际化**：支持多语言切换
- **个性化**：支持用户自定义工作台布局

### 2. 技术升级方向

- **组件库**：提取公共组件为独立组件库
- **微前端**：支持微前端架构，独立部署子应用
- **PWA**：支持离线访问和消息推送
- **低代码**：提供页面可视化配置能力

### 3. 体验优化方向

- **加载优化**：骨架屏、懒加载、预加载
- **动画优化**：适度的过渡动画提升体验
- **交互优化**：手势操作、快捷键支持
- **反馈优化**：操作反馈、错误提示优化

---

## 十一、文件清单

- `bim-middleware-framework.html` - 框架主文件
- `bim-middleware-framework-guide.md` - 使用说明文档（本文档）

---

## 十二、更新日志

### v1.0.0 (2026-01-07)
- 初始版本发布
- 完成总体框架设计
- 实现五大核心模块导航
- 完成首页工作台框架
- 提供完整的配置化菜单系统

---

**文档维护：** 深圳市BIM数据与能力中台产品团队  
**最后更新：** 2026-01-07  
**版本：** v1.0.0
