---
layout: post
title: Shopify tutorial
author: Andy Feng
---
# Introduction
Theme = 你网站的“外壳 + 布局系统”
它控制：
- 页面结构    
- 排版    
- 字体    
- 颜色    
- 产品展示方式    
- Header / Footer    
- 按钮样式    
- 动画效果    
Theme 不控制：
- 库存    
- 订单    
- 税    
- 付款方式    
- 客户数据   
这些是 Shopify 核心系统。
## OS 2.0 = Shopify Online Store 2.0 架构升级
核心改变：
以前：
- 只有首页可以自由添加 Section    
现在：
- 所有页面都可以添加 Section    
- 每个页面可以有不同结构    
- 支持 JSON 模板   
这就是：
> Sections Everywhere
## 主题结构
Shopify 主题的逻辑是：**Layout** 装着 **Template**，**Template** 由多个 **Section** 组成，**Section** 里面包含多个 **Block**，而 **Liquid** 负责把后台的数据填进这些框框里。
```
Layout
   ↓
Template
   ↓
Section
   ↓
Block
   ↓
Metaobject
```
主题 Shopify Admin > `Edit Code`（编辑代码），包括如下内容：
![](images/posts/20260304.jpeg)
```
layout → 网站骨架
templates → 页面类型
sections → 页面模块
blocks → 模块内部元素
snippets → 小组件
assets → 样式和脚本
config → 主题设置
locales → 翻译
```
### Layout (布局层)
这是网页的“外壳”。最核心的文件是 `theme.liquid`。
- 它包含了 HTML 的 `<head>` 和 `<body>` 标签。    
- 所有的页面（首页、产品页等）都是在这个文件内部渲染的。就像一个画框，中间的内容在变，但框（导航栏、页脚）是不变的。    
它定义：
- HTML 结构    
- 全站 Header, Footer    
- 资源加载（CSS / JS）    
- 引入页面模板内容
可以理解为“网站骨架”。
```
<html>
  <head>...</head>
  <body>
    header
    页面内容
    footer
  </body>
</html>
```
关键代码：
```
{{ content_for_layout }}
```
👉 它告诉 Shopify：
> 把当前页面模板内容插入到这里
### **Templates (模板层)**
决定了不同类型页面的结构。
- 在 OS 2.0 中，这些通常是 **JSON 文件**（如 `product.json`）。    
- 它们定义了页面上按顺序排列了哪些 **Sections**。
每个页面都有模板：
- product.json    
- collection.json    
- page.json    
- blog.json    
- article.json    
模板决定：
> 这个页面能使用哪些 Section
> Section 顺序
> 默认设置
e.g. product.json
```
{
  "sections": {
    "main": {
      "type": "product-information",
      "blocks": {
        "media-gallery": {
          "type": "_product-media-gallery",
          "static": true,
          "settings": {
            "media_presentation": "carousel",
            "media_columns": "two",
            "image_gap": 4,
            "large_first_image": false,
            "icons_style": "chevron_large",
            ...
          },
          "blocks": {}
        },
        "product-details": {
          "type": "_product-details",
          "settings": {
            ...
	       },
          "blocks": {
            "group_icgrde": {
              "type": "group",
              "name": "Header",
              "settings": {
				...
              },
	          "blocks": {            
		        ...
	          }
		  }
      ...
  }
}
```
> sections > block > nested block
### ** Sections (切片/模块层)** —— **最重要！**
这是 Shopify 装修最灵活的地方。
- **定义**：一个个独立的功能块（如：轮播图、特色产品列表、邮件订阅框）。    
- **特点**：商家可以在后台通过鼠标拖拽调整它们的顺序，或者修改里面的文字和图片，而不需要碰代码。
Section 是：
- header.liquid
- footer.liquid
- product-list.liquid
- product-information.liquid
- main-product.liquid
- featured-product.liquid
- image-banner.liquid
- carousel.liquid
- slideshow.liquid
- ...
每个 Section 是一个独立模块。
你在 Theme Editor 里拖动的就是 Section。
e.g.
```
{% schema %}
{
  "name": "Image Banner",
  "settings": [...],
  "blocks": [  
	{  
		"type": "heading",  
		"name": "Heading"  
	}  
  ]
}
{% endschema %}
```
这个 schema 定义：
- 编辑器里可以调什么    
- 可以添加什么 block
例如：
product-information.liquid / main_product.liquid 的内部结构，每个都是可移动的。
```
Section
 ├── Media Block
 ├── Title Block
 ├── Price Block
 ├── Variant Block
 ├── Buy Button Block
 ├── Description Block
 ├── Collapsible Block
```
header.liquid
```
- Logo Block    
- Navigation Block    
- Icon Block（Search / Cart）    
- Announcement Block
```
footer.liquid
```
- 添加多个菜单 Block    
- 添加 Newsletter Block    
- 添加 Social Block    
- 添加 Text Block
```
集合页结构
```
- Banner Section    
- Product Grid Section    
- Filter Block    
- Sorting Block
```
Grid 布局很灵活，可以：
- 控制列数    
- 控制卡片样式    
- 控制信息显示顺序
### **Blocks (区块层)**
Section 内部的更小单元，通常是liquid文件。
- 例如：一个“幻灯片”Section 里面会有多个“幻灯片图片”Block；一个“产品详情”Section 里面会有“标题”、“价格”、“购买按钮”等 Block。
Hero Section 里可以有：
- Heading Block    
- Button Block    
- Text Block    
Horizon 主题会大量使用 Block。
### ** Snippets (片段层)**
一些细小的、可重用的代码片段。
- 比如“产品小卡片”或“社交分享图标”。如果多个页面都要用到同一个小组件，我们会把它写在 Snippet 里，然后用 `{% render 'filename' %}` 调用。
包括
- product-card.liquid
- price.liquid
- icon.liquid
- ...
### ** Assets (资源层)**
存放主题用到的所有图片、字体、CSS 样式表和 JS 脚本文件。
包括
- base.css
- theme.js
- global.js
主题样式通常在base.css改
> 但不建议直接乱改. 因为：升级主题可能覆盖, 容易出错
### ** Config (配置层)**
存储你在后台“主题设置”里选定的全局颜色、字体方案等。
里面有两个重要文件：
```
settings_schema.json
settings_data.json
```
settings_schema.json
```
定义主题设置页面的结构
例如：
- 颜色    
- 字体    
- Logo 上传    
- 社交链接
```
settings_data.json
```
保存你当前主题的所有设置值
比如：
- 颜色选什么    
- Logo 是哪个文件    
- 首页用哪些 section
```
### locales
这是语言文件。
```
en.default.json
fr.json
```
里面是：
```
"cart.title": "Your cart"
```
用于多语言翻译。
## Theme Editor 工作原理
当你在：
Online Store → Customize
你其实在编辑：
JSON Template + Section Settings
所有设置都被保存成：
```
config/settings_data.json
```

| 维度          | Horizons系列                                        | **Trade**                                       | **Spotlight**                                       | **Refresh**                                       | **Dawn**                                       |
| :---------- | :------------------------------------------------ | :---------------------------------------------- | :-------------------------------------------------- | :------------------------------------------------ | :--------------------------------------------- |
| **发布年份**    | **2025年 (最新旗舰)**                                  | 2023年                                           | 2023年                                               | 2022年                                             | 2021年 (经典旗舰)                                   |
| **核心定位**    | **AI 驱动 & 模块化**                                   | **高效采购 & 工业目录**                                 | **极速启动 & 转化导向**                                     | **参数背书 & 强对比**                                    | **极简 & 高速基础款**                                 |
| **五金适配点**   | 智能家居、高端品牌硬件                                       | **螺丝零件、大批量批发**                                  | 单一明星产品、简单配件                                         | 精密仪器、手动工具                                         | 通用五金、单店起步                                      |
| **设计风格**    | 现代灵动、支持复杂布局                                       | 工业商务、信息密度极高                                     | **极其简约、聚焦产品**                                       | 硬朗、强调细节与品质                                        | 简洁留白、中性通用                                      |
| **搜索与过滤**   | **极佳 (AI 增强语义搜索)**                                | **最强 (多维规格参数过滤)**                               | 基础 (适合快速查找)                                         | 优秀 (侧重技术分类)                                       | 基础 (标准属性过滤)                                    |
| **购物车特色**   | 购物车内可直填折扣码                                        | **支持快速订购批量列表**                                  | **流线型、极简下单路径**                                      | 抽屉式，带产品建议                                         | 基础抽屉式购物车                                       |
| **SKU 支持度** | 中大型 (100 - 500)                                   | **极大型 (1000+)**                                 | **微型 (1 - 50)**                                     | 中小型 (10 - 200)                                    | 中型 (50 - 200)                                  |
| **自定义难度**   | 低 (嵌套块自由度极高)                                      | 中 (布局相对固定)                                      | **极低 (配置项精简)**                                      | 低 (填充内容即有质感)                                      | 中 (高级功能需依赖 App)                                |
| **技术架构**    | **OS 3.0 (最新组件化)**                                | OS 2.0 (稳定版)                                    | OS 2.0 (极致性能版)                                      | OS 2.0 (稳定版)                                      | OS 2.0 (行业标准)                                  |
| **下载地址**    | [点击下载](https://themes.shopify.com/themes/horizon) | [点击下载](https://themes.shopify.com/themes/trade) | [点击下载](https://themes.shopify.com/themes/spotlight) | [点击下载](https://themes.shopify.com/themes/refresh) | [点击下载](https://themes.shopify.com/themes/dawn) |
- **Trade** 适合做**“零件超市”**：如果你的客户经常需要一次性买 10 种不同规格的钻头。
- **Horizons** 适合做**“品牌旗舰”**：如果你卖的是像大疆、米家这种风格的智能五金。    
- **Refresh** 适合做**“专业工具店”**：如果你需要强调产品的扭矩、功率、材质等技术参数。    
- **Dawn** 适合做**“测款店”**：如果你还在摸索期，追求最快、最稳的上线速度。
- **Spotlight：** 如果你只想通过 TikTok 或社媒卖**一款**爆火的万能工具，追求极致的下单速度。
## 最佳实践
### 不要直接修改核心文件 `theme.liquid`，避免未来更新覆盖
```
替代方案：
- **创建 snippet**    
- **创建自定义 Section**    
- 在模板或现有 Section 中引入 snippet
- 这样即便主题更新，snippet 可以独立管理。
  
{% render 'custom-hero' %}
```
### 不要直接修改 `base.css`。
建议创建一个 `custom.css` 文件并在 `theme.liquid` 中引入，这样官方更新 `base.css` 时不会覆盖你的自定义样式。
### 使用自定义 Section
步骤：
1. `sections/` → 新建 `custom-section.liquid`    
2. 添加基本结构
3. 保存后即可在 Theme Editor 拖入页面
```json
{% schema %}
{
  "name": "Custom Section",
  "settings": [
    {
      "type": "text",
      "id": "heading",
      "label": "Heading"
    }
  ],
  "blocks": [],
  "presets": [
    {
      "name": "Custom Section"
    }
  ]
}
{% endschema %}

<div class="custom-section">
  <h2>{{ section.settings.heading }}</h2>
</div>
```
### 自定义 Block, Block = Section 内的小模块
```json
"blocks": [
  {
    "type": "button",
    "name": "CTA Button",
    "settings": [
      {"type": "text", "id": "button_text", "label": "Button Text"},
      {"type": "url", "id": "button_url", "label": "Button URL"}
    ]
  }
]
```
然后在 section 文件里渲染：
```
{% for block in section.blocks %}
  {% if block.type == 'button' %}
    <a href="{{ block.settings.button_url }}">{{ block.settings.button_text }}</a>
  {% endif %}
{% endfor %}
```
### **利用 GitHub 集成**：
通过 Shopify 的 GitHub 链接功能，你可以看到官方代码仓库的 Pull Request。在合并（Merge）时，你可以清晰地对比哪些是官方更新，哪些是你的自定义代码。
### 调用 Metaobject
Horizon 天生支持动态数据：
1. 创建 Metaobject，例如 Brand 信息    
2. 在 section/block 中调用：
```
{% assign brand = shop.metaobjects.brand.first %}
<h2>{{ brand.name }}</h2>
<p>{{ brand.description }}</p>
```
3. 可以结合 `for` 循环展示多条数据   
> 这样做比写死内容更灵活，也不会被主题更新覆盖
### 避免主题升级覆盖代码
原则：
1. **不要直接改官方 Section / Snippet**  
    → 复制一份自定义，重命名后使用    
2. **把自定义 CSS / JS 放在 assets/custom.css / custom.js**  
    → 在 theme.liquid 里单独引用    
3. **使用 snippet 和自定义 Section 代替修改官方文件**
## 导入自定义theme
![](images/posts/20260228-7.jpeg)
## 免费主题 vs 付费主题
本质一样。区别在：

| 项目   | 免费  | 付费            |
| ---- | --- | ------------- |
| 代码质量 | 官方高 | 看开发者          |
| 内置功能 | 少   | 多             |
| 促销组件 | 少   | 多             |
| 视觉预设 | 基础  | 丰富            |
| 价格   | 免费  | USD $200-$400 |
## Theme vs App
heme 负责：
- 显示    
App 负责：
- 功能扩展    
例如：
- 评论    
- 预购    
- 订阅    
- Upsell    
App 会通过：
- App block    
- Snippet    
- Script    
插入到主题里。
# Horizons
Horizon 是 Shopify  2025年推出的**新一代主题基石**[horizon themes](https://themes.shopify.com/collections/horizon-themes)。最大特色之一是它使用了 **全新的 Theme Blocks 系统**，比 Dawn 传统的 Section 更强大：
✅ **可嵌套块（Nested Blocks）**：可以将区块嵌套在其它区块里，最多可达 **8 层深**，页面结构自由度大得多。  
✅ **可复用模块**：同一个块可以在不同页面重复使用，不需要重复构建。  
✅ **拖放式设计**：可视化拖放，可创建复杂布局，无需写代码。
✅ **深层嵌套：** 传统的 Dawn 只能垂直堆叠 Section。Horizons 允许你**在一个 Section 内嵌套多达 8 层 Block**。
✅ **五金应用：** 你可以轻松创建一个“产品功能拆解图”区，将图片、文字、参数标签、甚至是小型视频块在一个区域内左右、上下灵活排列，而无需写代码。
✅  **全局块 (Global Blocks)：** 你可以创建一个“售后保障”块，在全店引用。修改一处，全店同步更新，非常适合五金行业频繁更新的保修条款。
换句话说，Horizon 不是**线性堆叠 Sections**，而是**像积木一样自由组合区块**，设计自由度大很多。

Horizon 提供了多种预设，似乎不一致？
- **Dwell (家居/生活方式)**：强调“慢生活”感，大面积留白，适合展示高客单价、需要情绪价值的产品。 
- **Glow (美妆/护肤)**：高对比度的色彩方案，内置细腻的微动效，重点突出成分表和用户评价。 
* **Fabric (时尚/服饰)**：支持瀑布流布局，优化了移动端滑动（Swiping）翻页体验，强调模特上身图。 
* **Ritual (保健/食品)**：色调温暖柔和，排版紧凑，侧重于订阅制（Subscription）和信任背书展示。 
* **Rise (运动/户外)**：高饱和度色彩，粗犷的字体排版，强调速度感和功能性参数。 
* **Dusk (夜间/科技)**：深色模式预设，适合高端电子产品或潮玩，营造神秘感和精密感。 
* **Azure (珠宝/奢侈品)**：极简边框设计，针对高分辨率细节图进行了锐化显示优化。 
* **Verge (快消/百货)**：侧重于大库存储备，导航栏支持复杂的 Mega Menu，搜索功能极其显眼。 
* **Canvas (艺术/手作)**：非对称网格布局，打破常规排版，适合充满设计感的单品展示。 
* **Sprout (母婴/园艺)**：色彩清新自然，强调安全性提示和社区内容展示。 
## 结构
Horizon 和 Dawn 最大区别是它使用 **Theme Blocks + Nested Blocks（嵌套区块）结构**。
传统主题（如 Dawn）
```
Page
 ├ Section
 ├ Section
 ├ Section
 
Section 里的 Block 就像排队一样，只能从上往下堆。如果你想让图片和文字左右并排，通常需要开发人员去改 CSS 代码。
```
Horizon 结构
```
Page
 ├ Section
     ├ Block
         ├ Nested Block
             ├ Nested Block
```
👉 深度嵌套 (Nested Blocks)：你可以在一个 Section 里面放 Block，Block 里面再放子 Block，最多可嵌套 8 层
👉  **CSS 网格引擎 (Grid Engine)：** 商家可以直接在后台通过可视化界面调整间距、对齐方式和比例，实现类似 Canva 或 Figma 的自由布局。
## 代码结构
Horizon 使用了 OS 2.0 的目录结构。
```
Horizon-Theme-Root/
├── assets/
│   ├── base.css             # 全局样式与变量
│   ├── theme-blocks.js      # 处理 Horizon 核心嵌套逻辑的脚本
│   ├── component-grid.css   # 处理弹性网格布局的专门样式
│   └── ...
├── blocks/                  # [新增/强化] 独立的区块定义文件
│   ├── hero-layer.liquid
│   └── promo-tag.liquid
├── layout/
│   └── theme.liquid         # 全局母版
├── sections/
│   ├── main-product.liquid  # 高度模块化的产品页 Section
│   └── header.liquid        # 支持多级 Mega Menu 的页头
├── snippets/
│   └── card-product.liquid  # 优化后的产品卡片片段
└── templates/
    ├── index.json           # 首页配置
    └── product.json         # 产品页配置
```
## 配置步骤
**Online Store → Themes → Customize** > 全局设置（Theme Settings）
![](images/posts/20260228.jpeg)
重点设置：
🔹 Branding（品牌）
- Logo    
- Favicon    
- 品牌色（Primary / Accent）    
- 按钮风格（圆角/直角）   
🔹 Typography（字体）
- 标题字体    
- 正文字体    
- 字号比例    
🔹 Layout
- 页面宽度    
- 图片比例    
- 产品卡片样式    
Theme Settings > **Colors**。
- **最佳实践**：Horizon 允许你设置最多 20 组配色方案。建议至少设置三组：
- **Scheme 1 (Light)**：纯白背景 + 深色文字（用于大部分展示区域）。
- **Scheme 2 (Accent)**：品牌主色背景 + 白色文字（用于吸引注意力的 CTA 按钮）。
- **Scheme 3 (Gradient)**：开启渐变背景（用于首页中间的“故事性”分区，增加高级感）。
## 搭建首页
Sections > Hero > Add Section -> Image Banner。
![](images/posts/20260228-1.jpeg)
### 首页的黄金结构建议
对于使用 Horizon 的品牌，推荐以下排列顺序：
1. **Announcement Bar**：一条简单的促销信息。    
2. **Hero Banner**：一张高质感的生活化大图 + 一个按钮。    
	- 大图或视频    
	- 强品牌标题    
	- 一句价值主张    
	- 一个 CTA 按钮    
	3 秒内让用户知道你卖什么。示例结构：
	```
	[Background Image]  
	Heading block: Elevate Your Everyday  
	Text block: Premium essentials for modern life  
	Button block: Shop Now
	```
		
3. **Marquee (跑马灯)**：Horizon 特有的滚动文字，用来展示“全场包邮”或“媒体报道”。  
4. 品牌价值模块（Why Choose Us）
	这一步解决信任问题。添加 3–4 个 Block：
	- Free Shipping    
	- 30-Day Guarantee    
	- Premium Quality    
	- Secure Checkout

5. **Featured Collection**：主推产品。 4–8 个主打产品，1-2行，统一比例。
6. **Image with Text (Editorial)**：介绍你的品牌初衷或核心技术。
7. **Footer**：包含社交媒体、订阅入口和信任标志。
#### 配置collection
![](images/posts/20260312.jpeg)
custom css:
```
/*背景渐变*/
.collection-card__content {
  color: #ffffff;
  font-size: 30px;
  text-shadow: 0 4px 12px rgba(0, 0, 0, 0.45);
  background-color: #00000000;
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0));
}

/*文字居中*/
.collection-card__content {
  position: absolute;
  inset: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  color: #ffffff;
  font-size: 26px;
  font-weight: 500;
  letter-spacing: 0.12em;

  text-shadow: 0 2px 8px rgba(0, 0, 0, 0.35);

  background: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.15),
    rgba(0, 0, 0, 0.15)
  );
}

/*背景模糊*/
.collection-card__content {
  position: absolute;
  inset: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  color: #ffffff;
  font-size: 26px;
  letter-spacing: 0.08em;
  font-weight: 500;

  text-align: center;

  background: rgba(0, 0, 0, 0.18);
  backdrop-filter: blur(2px);
}

/*背景模糊 + 文字边框*/
.collection-card__content {
  position: absolute;
  inset: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  color: #ffffff;
  font-size: 26px;
  letter-spacing: 0.08em;
  font-weight: 500;

  text-align: center;

  background: rgba(0, 0, 0, 0.18);
  backdrop-filter: blur(2px);

  text-shadow: -1px -1px 0 rgba(0, 0, 0, 0.6), 1px -1px 0 rgba(0, 0, 0, 0.6),
    -1px 1px 0 rgba(0, 0, 0, 0.6), 1px 1px 0 rgba(0, 0, 0, 0.6),
    0 3px 10px rgba(0, 0, 0, 0.35);
}
```
### 产品详细页结构
1️⃣ 图片区域
- 保持比例一致    
- 不要 10 张图    
- 5 张以内最佳
2️⃣ 标题 + 价格
顺序：
Title  
Rating  
Price  
Short Description
3️⃣ Variant 选择
Horizon 支持：
- Button 风格    
- 下拉风格    
建议：
小数量 → Button  
大数量 → Dropdown
4️⃣ Add to Cart 区域
必须包含：
- 库存提示    
- 发货时间    
- 退货保障
5️⃣ 折叠式说明
使用：
Collapsible Block
分为：
- Description    
- Shipping    
- Returns    
- Materials
不要把说明写成一大段。
### 集合页
进入：
Collection Template
建议：
- 开启 Filter    
- 控制每行产品数    
- 统一图片比例
- 不要每行 4 个以上（移动端体验差）。
# 代码开发与自定义theme
编写一个自定义 Section，其 Schema 结构如下
```
{% schema %}
{
  "name": "自定义弹性容器",
  "tag": "section",
  "class": "section",
  "settings": [
    {
      "type": "color_scheme",
      "id": "color_scheme",
      "label": "颜色方案",
      "default": "scheme-1"
    }
  ],
  "blocks": [
    {
      "type": "@theme" 
    }
  ],
  "presets": [
    {
      "name": "自定义弹性容器"
    }
  ]
}
{% endschema %}
```
* **关键点**：使用 `"type": "@theme"` 允许商家在你的 Section 内部放置任何 Horizon 主题自带的原子区块（如文本、图片、按钮），这是实现“8层嵌套”的代码基础。 
## CSS 修改：利用 CSS 变量实现全局样式覆盖
Horizon 极度依赖 CSS 变量（CSS Variables）来实现不同“颜色方案”的瞬间切换。你**不应**在 CSS 中写死颜色值。
* **Step 1：找到变量定义**
    打开 `assets/base.css`。你会看到类似 `--color-background` 或 `--color-button-text` 的定义。
* **Step 2：局部覆盖样式**
    如果你只想修改特定 Section 的外观，应该使用容器选择器结合变量：
    ```css
    .my-custom-section {
      background: rgb(var(--color-background));
      color: rgb(var(--color-foreground));
    }
    ```
* **Step 3：使用容器查询 (Container Queries)**
    Horizon 领先于 Dawn 的地方在于它使用 `@container` 而非 `@media`。这能保证当你的区块被拖到一个“侧边栏”或“三分之一分栏”里时，它能自动调整自己的排版。 
## 性能优化：保持 Horizon 的“全绿”跑分
Horizon 的初始性能非常出色，但自定义开发很容易破坏它。
* **Image 标签优化**：
    永远使用 `image_tag` 滤镜，并配合 `widths`, `sizes` 和 `loading: 'lazy'`。

```liquid
{{ section.settings.image | image_url: width: 1500 | image_tag: loading: 'lazy', sizes: '100vw', widths: '375, 550, 750, 1100, 1500' }}
```
# FAQ
## 如果编辑了theme，将来theme升级了，我的定制是不是失效了？
当 Shopify 发布新版本时：
- ✔ 不会自动覆盖你当前主题    
- ✔ 会在后台提示 “Update available”    
- ✔ 会生成一个“新版本副本”
注意 如果你点击了“编辑代码（Edit Code）”，并在以下文件中写了内容，升级时**会失效**：
- **Liquid 文件：** 比如你在 `product.liquid` 里手动加入了一段自定义代码。    
- 修改了代码（Liquid / CSS / JS）
	比如：
	- 改 product.liquid    
	- 改 header.liquid    
	- 改 sections 文件    
	- 添加自定义 JS    
	- 修改 cart 逻辑
- **CSS/JS 文件：** 比如你在 `base.css` 底端写的自定义样式。    
- **第三方插件手动安装的代码：** 早期一些插件需要你手动把代码粘贴到主题中，这些代码在升级后需要重新搬运。
### 如何在升级时不丢失定制？（最佳实践）
为了应对像 **Horizons** 这种更新频繁的新主题，建议采用以下方案：
尽量**只通过编辑器（Customize）和 Custom CSS 框**来做调整。这样当你未来想要享受 Shopify 发布的最新 AI 功能或速度优化时，一键升级几乎是无损的。
#### A. 使用“自定义 CSS”版块 (推荐)
在 Shopify 后台编辑器中，每个 Section 甚至整个主题设置里都有一个 **"Custom CSS"** 文本框。
- **优点：** 这里的代码是保存在数据库里的，**主题升级时会自动迁移**，不会丢失。    
- **建议：** 除非必要，尽量不要去改动 `theme.css` 文件。
#### B. 利用主题 App 嵌入 (App Embeds)
现在的插件（App）大多使用“嵌入”模式，不直接改动你的代码文件。升级主题后，你只需要在后台重新“开关”一下 App 即可恢复功能。
#### C. 记录你的“代码补丁”
如果你必须修改代码：
1. **建立一个文档：** 记录下你修改了哪个文件、哪一行、代码是什么。    
2. **使用注释：** 在代码中加上 `/* CUSTOM START - [Date] */` 这样的标记，方便搜索和搬运。
## 只用编辑器（推荐方式）
你只是：
- 调整颜色    
- 添加区块    
- 改文案    
- 调布局    
- 改字体
👉 这些属于 **Theme Settings + JSON 配置**
✅ 升级后大概率可以自动迁移  
✅ 不会失效  
✅ 不影响
# References
[https://help.shopify.com/en/](https://help.shopify.com/en/)
[https://www.youtube.com/watch?v=ferhOYx1NMo](https://www.youtube.com/watch?v=ferhOYx1NMo)
[https://www.youtube.com/watch?v=yHHh4AmV1eg](https://www.youtube.com/watch?v=yHHh4AmV1eg)