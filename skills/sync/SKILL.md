---
name: wiki:sync
description: 全量解析项目代码并生成完整 wiki（sync 命令）
---

# Wiki Sync

全量解析项目代码，生成完整的 `.wiki/` 知识库。

## 前置条件

- `.wiki/` 目录已存在（如果不存在，提示先运行 `/wiki:init`）

## 执行步骤

1. **读取 `.wiki/config.json`** 获取 include/exclude 规则和 last_sync_commit

2. **确定增量起点**
   - 如果 `last_sync_commit` 存在，用它作为起点：`git diff <last_sync_commit>...HEAD`
   - 否则全量扫描

3. **扫描项目文件**
   - 按 include 规则扫描项目
   - 按 exclude 规则过滤文件（node_modules, .venv, vendor, __pycache__ 等依赖/缓存目录不读）
   - 按目录/模块归类文件

4. **识别项目入口**
   - 查找 main, index, app, server, serve, cli, main.go 等入口文件
   - 确定项目类型（TypeScript/JS/Python/Go）

5. **按模块生成 wiki 页面**
   - 每次 LLM 调用处理一个模块
   - 输出到 `wiki/modules/<模块名>.md`
   - API 路由输出到 `wiki/apis/<模块名>.md`
   - 数据模型输出到 `wiki/models/<模块名>.md`

6. **生成 `wiki/index.md`** 汇总所有模块

7. **如果 `schema/system.md` 缺失，生成默认模板**

8. **更新 `last_sync_commit`**
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
