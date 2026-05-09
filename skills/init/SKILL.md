---
description: 在当前项目创建 .wiki/ 知识库骨架（init 命令）
---

# Wiki Init

在当前项目根目录创建 `.wiki/` 知识库骨架。

## 执行步骤

1. **检查是否已存在 `.wiki/`**
   - 如果存在，提示用户已初始化，无需重复操作

2. **创建目录结构**
   ```
   .wiki/
     config.json
     schema/
       system.md
     wiki/
       index.md
       modules/
       apis/
       models/
       flows/
   ```

3. **生成 `.wiki/.gitignore`**
   ```
   **/*
   ```
   忽略 `.wiki/` 内部所有内容，避免 wiki 被 git 追踪。

4. **生成 `config.json`**
   ```json
   {
     "version": 1,
     "include": ["src/**/*.{ts,js,py,go}"],
     "exclude": [
       "node_modules", "dist", ".git", ".venv", "venv", "env",
       "vendor", "__pycache__", ".pytest_cache", "*.test.ts", "*.spec.ts",
       "_test.go", "*.md", "build", "*.lock", "package-lock.json"
     ],
     "schemaVersion": "1"
   }
   ```

   **exclude 说明**：
   - `.venv`, `venv`, `env` — Python 虚拟环境
   - `vendor` — Go/PHP/其他语言的依赖目录
   - `node_modules` — Node.js 依赖
   - `__pycache__`, `.pytest_cache` — Python 缓存
   - `*.lock`, `package-lock.json` — 锁定文件（不读内容）

5. **生成 `schema/system.md`**（核心文件，告知 LLM 如何解析代码）

   ```
   你是项目代码维基生成器。

   ## 1. 项目扫描规则
   - 入口文件：main, index, app, server, serve, cli, main.go 等
   - 模块拆分：按功能/目录划分
   - API/路由：按 HTTP method + path
   - 数据模型：Schema、struct、class、interface 定义

   ## 2. Wiki 页面结构（每个模块页必须包含）
   - 模块职责（一句话）
   - 核心文件路径
   - 关键函数/类（名称 + 用途）
   - 依赖模块（引用其他 wiki 页面）
   - 重要流程（如果是 API 则标注 method + path）

   ## 3. 输出格式
   - 纯 Markdown
   - 人可读可编辑
   - 模块间引用：[模块名](file:模块.md)
   - ⚠️ 标注存疑或矛盾之处

   ## 4. /wiki update 策略
   - 变更文件 → 判断属于哪个 wiki 模块页
   - 新增 → 补充对应 wiki
   - 修改 → 替换对应段落
   - 删除 → 标记 [过时]
   ```

6. **生成空的 `wiki/index.md`**
   ```markdown
   # 项目 Wiki

   > 自动生成，请勿手动删除

   ## 模块

   ## API

   ## 数据模型

   ## 业务流程
   ```

## 输出

完成后提示用户：
```
✅ .wiki/ 知识库已初始化
   - config.json: 包含/排除规则
   - schema/system.md: LLM 代码解析规则
   - wiki/index.md: 项目总览页

下一步：运行 /wiki:sync 生成完整 wiki
```
