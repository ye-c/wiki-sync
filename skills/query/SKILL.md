---
name: wiki:query
description: 基于 wiki 知识库回答项目问题（query 命令）
---

# Wiki Query

基于 `.wiki/` 知识库回答用户关于项目的提问。

## 前置条件

- `.wiki/` 目录已存在
- `wiki/index.md` 存在

## 执行步骤

1. **读取 `wiki/index.md`** 了解项目整体结构

2. **定位相关 wiki 页面**
   - 根据用户问题关键词匹配模块页
   - 读取相关 `wiki/modules/`, `wiki/apis/`, `wiki/models/` 页面

3. **LLM 综合回答**
   - 基于 wiki 内容回答
   - 标注引用来源：`来源：wiki/wiki/xxx.md`

## 输出要求

- 直接回答用户问题
- 引用相关 wiki 页面
- 如果 wiki 内容不足，注明"根据现有 wiki 信息无法完全回答此问题"
- 静默模式：不展示查询过程

## 示例

用户问："用户模块的核心函数是什么？"

回答应基于 `wiki/modules/user.md` 内容，并引用来源。
