<div align="center">

# UserThoughts.SKILL

[![](https://img.shields.io/badge/Copyright-JularDepick-0066AA)](./COPYRIGHT)
[![](https://img.shields.io/badge/License-MIT-yellow)](./LICENSE)
[![](https://img.shields.io/badge/Standard-Agent--SKILL-red)](https://agentskills.io/)

[[English]](./README.md)
[[简体中文]](./README_zh-CN.md)

为Agent创建的一个SKILL技能，能够让Agent自动组织用户发言，并整理进一个绑定于项目生命周期的用户想法文档库，以此约束Agent在项目目录下的工作规范、确保Agent遵守用户的观点、想法、意图。

</div>

---

## 技能亮点

- 让Agent自主维护、挂载用户想法文档库
- 利用提示词约束解决Agent容易忽略用户需求细节的痛点

---

## 安装技能

- 前置依赖：
  - `read/write` 读写工具/技能
  - `bash` 执行命令的工具/技能
- 搭配建议：
  - `websearch` 网络搜索技能

---

## 技能使用

- 技能一经安装自动启用，无需手动开启
- 技能命令(正则)：
```bash
```

---

## 技能结构
```bash
- SKILL/
  - SKILL.md # SKILL入口
  - scripts/ # 工具/脚本
  - assets/ # 模板/资源
    - Runtime-Template/ # SKILL运行时在工作目录创建的用户想法文档库的模板
```
---

## 运行时结构

---
