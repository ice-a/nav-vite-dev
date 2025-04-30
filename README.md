# 导航站点（nav_to_dev）

## 项目简介
本项目是一个基于 Vue 3 和 Ant Design Vue 构建的现代化导航站点，旨在为开发者和用户提供便捷、可扩展的常用工具与网站入口集合。项目采用 Vite 作为构建工具，拥有快速的开发体验和优秀的性能表现。

## 主要功能
- 支持自定义导航内容，轻松扩展站点链接
- 现代化响应式界面，适配多种终端
- 集成 Ant Design Vue 组件库，界面美观易用
- 快速搜索与分类导航

## 技术栈
- [Vue 3](https://vuejs.org/)：渐进式 JavaScript 框架，采用 Composition API
- [Vite](https://vitejs.dev/)：极速前端构建工具
- [Ant Design Vue](https://www.antdv.com/)：企业级 UI 组件库

## 安装与运行
1. **克隆项目**
   ```bash
   git clone <your-repo-url>
   cd nav_to_dev
   ```
2. **安装依赖**
   ```bash
   npm install
   ```
3. **启动开发环境**
   ```bash
   npm run dev
   ```
   启动后访问 [http://localhost:5173/](http://localhost:5173/) 查看效果。
4. **打包构建**
   ```bash
   npm run build
   ```

## 目录结构说明
```
nav_to_dev/
├── public/                # 公共资源目录
├── src/                   # 源码目录
│   ├── assets/            # 静态资源
│   ├── components/        # Vue 组件
│   ├── data/              # 导航数据与配置
│   ├── main.js            # 入口文件
│   ├── style.css          # 全局样式
├── index.html             # 入口 HTML
├── package.json           # 项目依赖与脚本
├── vite.config.js         # Vite 配置
└── README.md              # 项目说明文档
```

## 自定义与扩展
- **添加/修改导航内容**：编辑 `src/data/navData.js` 或 `src/data/tools.json` 文件，按照现有格式添加新的导航项。
- **自定义样式**：可在 `src/style.css` 或各组件内修改样式，支持 CSS 预处理器扩展。
- **组件扩展**：在 `src/components/` 目录下新增或修改 Vue 组件，提升站点功能。

## 反馈与贡献
欢迎提交 Issue 或 Pull Request 参与项目完善。
