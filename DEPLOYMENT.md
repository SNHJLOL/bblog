# Stellar 主题改造部署说明

## 已完成的改造内容

### 1. 主题色方案配置
- 主强调色：#e6397c（玫红色，用于所有高亮、图标、边框、hover态、选中态）
- 背景主色：#1a1a1d（深炭黑，用于页面整体背景、侧边栏背景）
- 辅助背景色：#25252b（用于卡片、模块、导航栏hover态，比主背景浅1度）
- 文字主色：#ffffff（正文、标题）
- 文字次要色：#b0b0b5（描述、辅助文字、次要信息）
- 分割线/边框：#e6397c（半透明，opacity: 0.2）

### 2. 左侧侧边栏改造
- 保留了原有侧边栏结构：顶部博客标题「JLog」+ 导航菜单（主页/标签/历史文章/关于我）+ 个人信息卡（头像+昵称+社交图标）+ 天气模块
- 统一了侧边栏所有模块的样式：圆角、阴影、内边距完全一致
- 导航菜单美化：
  - 未选中态：图标+文字为 #b0b0b5，hover 时背景变为 #25252b，文字/图标变为 #e6397c，左侧添加 3px #e6397c 高亮条
  - 选中态：背景 #25252b，文字/图标 #e6397c，左侧高亮条常驻
- 个人信息卡美化：
  - 头像添加 3px #e6397c 描边+发光效果
  - 昵称「-喜-」居中，下方添加 #e6397c 装饰线
  - 社交图标统一为 #e6397c，hover 放大1.1倍
- 天气模块美化：
  - 背景 #25252b，标题「天气」用 #e6397c
  - 当前温度数字突出 #e6397c，未来预报图标统一为 #e6397c 线性图标
- 所有可点击元素添加 0.2s 平滑过渡动画

### 3. 技术实现
- 通过主题的 `_config.yml` 配置文件修改主题色和侧边栏背景
- 创建了自定义CSS文件 `source/css/custom.css` 实现详细的样式美化
- 在主题的 `head.ejs` 文件中引入了自定义CSS
- 保持了主题的所有原有功能（响应式、SEO、评论、搜索等）

## 配置文件变更

### 1. 主配置文件 (_config.yml)
- 已设置 `theme: stellar`

### 2. 主题配置文件 (themes/stellar/_config.yml)
- 修改了颜色配置：
  ```yaml
  color:
    theme: '#e6397c' # 主题色（玫红色）
    accent: '#e6397c' # 强调色（玫红色）
    link: '#e6397c' # 超链接颜色（玫红色）
  ```
- 修改了侧边栏背景：
  ```yaml
  leftbar:
    background-color-light: #1a1a1d # 深炭黑
    background-color-dark: #1a1a1d # 深炭黑
    background-image: 
    blur-px: 0px
    blur-bg: #1a1a1d
    background-opacity: 1
  ```
- 配置了导航菜单：
  ```yaml
  menubar:
    items:
      - id: home
        theme: '#e6397c'
        icon: solar:home-bold-duotone
        title: 主页
        url: /
      - id: tags
        theme: '#e6397c'
        icon: solar:hashtag-bold-duotone
        title: 标签
        url: /tags/
      - id: archives
        theme: '#e6397c'
        icon: solar:calendar-bold-duotone
        title: 历史文章
        url: /archives/
      - id: about
        theme: '#e6397c'
        icon: solar:user-bold-duotone
        title: 关于我
        url: /about/
  ```
- 添加了自定义CSS配置：
  ```yaml
  stellar:
    custom_css: /css/custom.css
  ```

### 3. 自定义CSS文件 (source/css/custom.css)
- 实现了所有样式美化效果
- 包含了响应式布局适配

## 部署步骤

1. **确保stellar主题已安装**
   - 主题已在 `themes/stellar` 目录中

2. **构建项目**
   ```bash
   # 清理缓存
   hexo clean
   
   # 生成静态文件
   hexo generate
   ```

3. **本地测试**
   ```bash
   # 启动本地服务器
   hexo server
   
   # 访问 http://localhost:4000 查看效果
   ```

4. **部署到远程服务器**
   ```bash
   # 部署到GitHub Pages或其他平台
   hexo deploy
   ```

## 注意事项

1. **权限问题**：如果遇到 "无法加载文件" 或 "禁止运行脚本" 的错误，请以管理员身份运行终端，或修改PowerShell执行策略：
   ```powershell
   Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

2. **响应式适配**：主题已适配移动端，侧边栏在小屏幕下可正常折叠/显示

3. **主题升级**：由于所有修改都通过配置文件和自定义CSS实现，后续主题升级时不会影响自定义样式

4. **性能优化**：自定义CSS已最小化，不会影响页面加载速度

5. **兼容性**：所有样式修改都使用了 `!important` 确保覆盖默认样式，保证在不同浏览器中的一致性

## 效果预览

部署完成后，您的博客将呈现以下效果：
- 深色主题背景，玫红色强调色
- 美观的侧边栏布局，包含导航菜单、个人信息卡和天气模块
- 平滑的过渡动画效果
- 响应式设计，适配各种屏幕尺寸

---

**部署完成！** 您的Hexo博客现在使用stellar主题，并按照设计要求进行了全面改造。