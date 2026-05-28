<div align="center">

# UserThoughts.SKILL

[![Copyright](https://img.shields.io/badge/Copyright-JularDepick-0066AA)](./COPYRIGHT)
[![License](https://img.shields.io/badge/License-MIT-yellow)](./LICENSE)
[![Standard](https://img.shields.io/badge/Standard-Agent--SKILL-red)](https://agentskills.io/)

[[English]](./README.md) [[简体中文]](./README_zh-CN.md)

一个遵循 [Agent Skills](https://agentskills.io/) 规范的技能，让 AI Agent 自动组织用户发言，维护绑定于项目生命周期的用户想法文档库 (mdbase)。

</div>

---

## 为什么需要这个技能？

与 Agent 协作开发项目时，用户会在对话中不断表达设计决策、需求偏好、规则约束。这些细节容易散落在聊天记录中。UserThoughts.SKILL 解决这个问题：

- **捕获**：对话中自动识别并记录用户想法
- **组织**：按维度整理为结构化文档库 (mdbase)
- **保留**：保持用户原始表述，不简化、不改写
- **绑定**：想法文档库与项目生命周期同步

---

## 安装

### 前置依赖

| 依赖 | 必需 | 用途 |
|------|------|------|
| `read` / `write` | 是 | 读写 `#ustht/` 下文件 |
| `bash` | 是 | 文件复制、目录创建 |
| SubAgent | 否 | 并行维护 mdbase 维度文件 |

### 安装方式

将 `user-thoughts/` 目录放入 Agent 的技能文件夹。具体路径取决于你的 Agent：

| Agent | 技能目录 |
|-------|---------|
| VS Code / Copilot | `.agents/skills/user-thoughts/` |
| Claude Code | `.claude/skills/user-thoughts/` |
| OpenCode | `.opencode/skills/user-thoughts/` |
| 其他 | 参考对应 Agent 文档 |

安装后，Agent 通过 `SKILL.md` 的 YAML frontmatter 自动发现技能。

---

## 快速开始

1. **安装**技能（见上方）
2. **开始对话**——当用户表达项目想法时，技能自动激活
3. **使用命令**：
   - `/ustht status` — 查看当前状态
   - `/ustht sortin` — 整理想法到 mdbase
   - `/ustht mdbase show` — 查看想法库

---

## 工作原理

技能按渐进式披露（progressive disclosure）分三阶段加载：

1. **发现** — Agent 读取 frontmatter 的 `name` 和 `description`，判断何时激活
2. **激活** — Agent 加载 `SKILL.md` 正文（核心指令，约 200 行）
3. **执行** — Agent 按需加载 `references/` 下的详细规范

### 工作流

```
用户发言 → Agent 识别项目想法 → 写入 raw/（即时计划）
         ↓
用户执行 /ustht sortin → Agent 处理 raw/ → 按维度追加到 mdbase/
         ↓
用户执行 /ustht mdbase show → Agent 展示组织好的想法库
```

---

## 技能结构

```
user-thoughts/
├── SKILL.md                    # 入口（frontmatter + 核心指令）
├── references/                 # 详细规范（按需加载）
│   ├── commands.md             # 命令正则与自然语言映射
│   ├── sortin.md               # 维护算法与维度管理
│   ├── edge-cases.md           # 边界场景与交互示例
│   └── safety.md               # 安全边界与数据完整性
└── assets/
    └── Runtime-Template/       # 首次使用时复制到工作目录的模板
        ├── define.ini
        └── mdbase/
            ├── README.ai.md
            ├── backlog.md
            └── details/...
```

---

## 命令

```bash
# 状态与开关
/ustht init                                # 初始化工作目录（创建 .ustht/ 及模板）
/ustht status                              # 全部状态概览
/ustht skill [on|off]                      # 查看/切换技能状态
/ustht instant [on|off]                    # 查看/切换即时计划

# 维护流程
/ustht sortin [--dry]                      # 软维护（追加新想法），--dry 预览不写入
/ustht resort [--dry]                      # 硬维护（重整全部 mdbase），--dry 预览不写入

# 忽略管理
/ustht ignore start|end                    # 开始/结束忽略区间（仅上下文有效）
/ustht ignore [--last]                     # 忽略上一条已记录的想法
/ustht ignore show                         # 查看被忽略的记录
... /ustht ignore                          # 后缀模式，忽略本条消息

# 内容查看与导出
/ustht raw                                 # 查看未处理的 raw 记录
/ustht mdbase show [--all|--维度名]        # 查看索引或指定维度
/ustht mdbase export [--all|--维度名]      # 导出到 #export/
/ustht import <路径>                       # 扫描路径下 .md 文件，并入 mdbase
```

---

## 运行时结构

首次使用后，技能在工作目录下创建 `.ustht/`：

```
<工作目录>/
└── .ustht/
    ├── define.ini             # 状态与宏常量
    ├── raw/                   # 用户原始发言（按日期分片）
    │   └── yyyy-mm-dd.md
    ├── ignored/               # 被忽略的发言
    │   └── yyyy-mm-dd.md
    ├── export/                # 导出的 mdbase 内容
    └── mdbase/                # 整理后的用户想法库
        ├── README.ai.md       # 索引与概览
        ├── backlog.md         # 待办事项
        └── details/           # 按维度组织的想法文件
            ├── rules.md       # 项目规则
            ├── plans.md       # 项目规划
            ├── dev-stack.md   # 技术栈
            ├── general.md     # 通用（兜底维度）
            ├── ui/
            │   ├── outline.md # UI 整体设计
            │   └── details.md # UI 细节设计
            └── ...            # 按需扩展
```

---

## 许可证

[MIT](./LICENSE) — Copyright (c) 2026 JularDepick
