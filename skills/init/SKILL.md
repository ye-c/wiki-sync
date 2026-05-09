---
description: 全量解析项目代码并生成完整 wiki
---

# Wiki Init

在当前项目根目录创建 `.wiki/` 知识库骨架，并全量生成 wiki。

## 执行步骤

1. **检查是否已存在 `.wiki/`**
   - 如果存在，跳过骨架创建，直接全量生成
   - 如果不存在，创建完整骨架

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

   ## 4. /wiki sync 策略（增量更新）
   - 变更文件 → 判断属于哪个 wiki 模块页
   - 新增 → 补充对应 wiki
   - 修改 → 替换对应段落
   - 删除 → 标记 [过时]
   ```

6. **生成 `wiki/index.md`**
   ```markdown
   # 项目 Wiki

   > 自动生成，请勿手动删除

   ## 模块

   ## API

   ## 数据模型

   ## 业务流程
   ```

7. **全量扫描项目文件**
   - 按 include 规则扫描项目
   - 按 exclude 规则过滤文件
   - 按目录/模块归类文件

8. **识别项目入口**
   - 查找 main, index, app, server, serve, cli, main.go 等入口文件
   - 确定项目类型（TypeScript/JS/Python/Go）

9. **按模块生成 wiki 页面**
   - 每次 LLM 调用处理一个模块
   - 输出到 `wiki/modules/<模块名>.md`
   - API 路由输出到 `wiki/apis/<模块名>.md`
   - 数据模型输出到 `wiki/models/<模块名>.md`

10. **生成 `wiki/index.md`** 汇总所有模块

11. **更新 `last_sync_commit`**
    - 同步完成后，将 config.json 中的 `last_sync_commit` 更新为当前 HEAD commit

## 输出格式

每个模块页必须包含：
- 模块职责（一句话）
- 核心文件路径
- 关键函数/类（名称 + 用途）
- 依赖模块（引用其他 wiki 页面）
- 重要流程（如果是 API 则标注 method + path）

模块间引用格式：`[模块名](file:模块.md)`

## 提示

- 大项目分批处理，避免单次 LLM 调用超时
- 标注存疑或矛盾之处用 ⚠️
- 生成完成后提示用户共生成了多少模块
