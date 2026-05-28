---
name: user-thoughts
description: 自动组织用户发言，维护绑定于项目生命周期的用户想法文档库(mdbase)。当用户表达项目想法、设计决策、需求偏好、规则约束时使用；当用户提到"想法"、"记录"、"维护"、"mdbase"、"sortin"时使用。即使用户没有明确要求记录，只要发言中包含对项目有意义的想法，就应激活此技能。
license: MIT. LICENSE has complete terms
compatibility: Requires read/write and bash tools. Optional SubAgent for parallel maintenance.
metadata:
  author: JularDepick
  version: "0.1.0"
  source: "https://github.com/JularDepick/UserThoughts.SKILL"
allowed-tools: read write bash
---

# UserThoughts.SKILL

## 重要声明

- **本 SKILL** 系指本文档及同目录下内容，整体称 `UserThoughts.SKILL`
- **用户**：使用、调用、提及本 SKILL 的发言人
- **行为边界**：不影响用户发言的指令性内容。SKILL 只做想法分析和记录，其他指令由 Agent 正常执行

---

## 目录定义

- `@/`：SKILL 安装目录（SKILL.md 所在目录，即 `user-thoughts/`）
- `~/`：工作目录
- `#ustht/` = `~/.ustht/`
- `#mdbase/` = `~/.ustht/mdbase/`
- `#ignored/` = `~/.ustht/ignored/`
- `#raw/` = `~/.ustht/raw/`
- `#export/` = `~/.ustht/export/`

---

## #ustht 目录结构

```
.ustht/
├── define.ini           # SKILL 状态与宏常量
├── raw/                 # 用户原始发言(按日期分片)
│   └── yyyy-mm-dd.md
├── ignored/             # 被标记忽略的发言
│   └── yyyy-mm-dd.md
├── mdbase/              # 整理后的用户想法库
│   ├── backlog.md       # 待办事项
│   ├── README.ai.md     # mdbase 索引与概览
│   └── details/         # 按维度组织的想法文件
│       ├── rules.md
│       ├── plans.md
│       ├── ui/
│       │   ├── outline.md
│       │   └── details.md
│       ├── dev-stack.md
│       ├── general.md   # 通用（不属于其他维度的想法）
│       └── ...          # 按需扩展
└── export/              # 从 mdbase 导出的内容
```

---

## 工具与环境

依赖（本 SKILL 不提供）：
- **read/write**（必需）：读写 `#ustht/` 下各文件
- **bash**（必需）：文件复制、移动、删除、目录创建
- **SubAgent**（可选）：并行维护 mdbase 维度文件

SubAgent 可用时**必须使用**，不得由主 Agent 自行维护。SubAgent 不可用时主 Agent 才直接执行。

必需工具缺失时向用户发出警告并暂停 SKILL 功能。

---

## 语法定义

- `&[keyname]`：引用 `#ustht/define.ini` 中 keyname 的值
- `&[keyname]=value`：将 define.ini 中 keyname 的值修改为 value

| 键名 | 类型 | 说明 |
|------|------|------|
| SKILL_STATUS | on\|off | 技能启用状态 |
| INSTANT_STATUS | on\|off | 即时计划启用状态 |
| LAST_SORTIN | yyyy-mm-dd HH:MM | 上次 sortin 时间戳 |

---

## 技能命令

命令以 `/ustht` 引导。完整正则语法和自然语言映射见 [references/commands.md](references/commands.md)。

### 状态与开关

- `/ustht init` — 初始化工作目录（创建 `.ustht/` 及模板）
- `/ustht status` — 输出全部状态
- `/ustht skill` — 输出技能状态
- `/ustht skill on|off` — 开启/关闭技能
- `/ustht instant` — 输出即时计划状态
- `/ustht instant on|off` — 开启/关闭即时计划

### 维护流程

- `/ustht sortin [--dry]` — 软维护（追加新想法），`--dry` 预览不写入
- `/ustht resort [--dry]` — 硬维护（重整全部 mdbase），`--dry` 预览不写入

### 忽略管理

- `/ustht ignore start|end` — 开始/结束忽略区间（仅上下文有效）
- `/ustht ignore --last` — 忽略上一条已记录的想法
- `.*/ustht ignore` — 后缀模式，忽略本条消息的想法（不记入 raw）

### 内容查看与导出

- `/ustht raw` — 查看未处理的 raw 记录
- `/ustht mdbase show [--all|--维度名]` — 查看索引或指定维度
- `/ustht mdbase export [--all|--维度名]` — 导出到 `#export/`
- `/ustht import <路径>` — 扫描路径下 .md 文件，并入 mdbase

**触发规则：** 用户发言匹配命令，或自然语言意图明确指向唯一命令时触发。详细映射见 [references/commands.md](references/commands.md)。

**链式命令：** 使用 `&&` 连接多条命令，按顺序依次执行。如 `/ustht skill on && instant on`。

---

## 即时计划

当 `&[INSTANT_STATUS]` 为 `on` 时自动执行：

1. **识别**：判断用户发言是否包含项目想法、决策、需求、规则、偏好
2. **制订计划**：写入 `#raw/` 当天日期.md，格式 `- [HH:MM] 想法原文 | 待归入:预判维度`
3. **不执行**：不改动 mdbase，延迟到 sortin 时统一执行
4. **过滤**：忽略区间内或末尾携带 `/ustht ignore` 的发言不写入
5. **不中断**：后台静默执行，不打断正常对话

---

## 维护流程

流程入参：`sortin` 软维护（追加）| `resort` 硬维护（重整）。

1. 读取 `#raw/` 未处理条目
2. 逐条分析归属维度
3. 追加（soft）或重整（hard）mdbase 对应文件
4. 标记已处理 raw 文件（`<!-- processed -->`）
5. 更新 LAST_SORTIN 时间戳和 README.ai.md 索引
6. 输出摘要

维度文件管理、raw 状态机制详见 [references/sortin.md](references/sortin.md)。

---

## 第一次使用

当工作目录下不存在 `.ustht/` 时，Agent 自动触发 `/ustht init`，或用户手动执行：

1. 在 `~/` 创建 `.ustht/` 目录
2. 复制 `@/assets/Runtime-Template/` 全部内容到 `#ustht/`
3. 用 bash 创建 `#raw/`、`#ignored/`、`#export/` 目录（若模板未包含）
4. 确认 `define.ini` 包含完整键值（SKILL_STATUS、INSTANT_STATUS、LAST_SORTIN）
5. 输出初始化确认：目录结构概览 + 可用命令列表

已存在 `.ustht/` 时执行 `/ustht init`：输出提示"已初始化"，不覆盖。

---

## 工作模式

- **被动模式**（默认）：仅响应技能命令
- **即时计划模式**：自动识别用户想法并写入 `#raw/`，mdbase 写入延迟到 sortin
- **忽略模式**：`ignore start`/`end` 区间内发言不记入

模式组合：即时计划 + 忽略可同时生效。被动/即时计划通过 define.ini 持久化；忽略区间仅上下文有效。

---

## 日常决策

- **用户发言优先**：明确表述都应记录，不得以 Agent 判断为由忽略
- **不过度推断**：只记录用户明确表达或可直接推导的想法
- **保持原文**：保留用户原始表述，不简化、不改写、不丢失细节
- **维度归类**：优先归入已有维度，无合适维度则归入 `general.md`
- **冲突处理**：以最新发言为准，原记录标注被替代并附日期
- **非项目内容不记录**：闲聊、与项目无关的话题不记入想法
- **单条多想法拆分**：一条消息包含多个独立想法时，拆分为多条记录

---

## Gotchas

以下是 Agent 容易犯的错误，务必注意：

- **ignore 区间不持久化**：`ignore start` 仅在当前上下文有效，跨会话自动失效
- **命令与想法共存时**：先执行命令，再做想法记录，顺序不可颠倒
- **raw 的 `<!-- processed -->` 标记**：必须在文件第一行，不能放在其他位置
- **sortin 不锁文件**：依赖 Agent 协调，sortin 期间新发言正常记入 raw
- **不主动删除维度文件**：用户明确要求时才标记 `<!-- deprecated -->`
- **想法 + 命令触发词**：不要误将触发词当作想法内容记录
- **resort 模式**：不是只追加，而是去重、归类、合并，必要时调整结构
- **ignore --last 无上一条**：返回提示，不报错
- **维度名验证**：仅 `[a-z0-9-]`，含 `..`、`/`、`\` 的参数必须拒绝
- **define.ini 写入**：值不得含换行符或 `=`，整文件覆写不追加
- **通用兜底**：无法归入已有维度的想法追加到 `general.md`，不轻易新建维度

---

## 边界场景

- **SKILL 关闭后**：文件保留，写入类命令返回提示，只读命令仍可用
- **跨会话恢复**：读取 define.ini 恢复状态，忽略区间不恢复
- **多项目隔离**：各工作目录独立 `.ustht/`，互不影响

更多边界场景和完整交互示例见 [references/edge-cases.md](references/edge-cases.md)。

---

## 安全边界

- **路径安全**：维度名仅允许 `[a-z0-9-]`，禁止 `..`、`/`、`\`，所有操作限制在 `#ustht/` 内
- **内容安全**：想法原文保留不转义，`<!-- processed -->` 仅检查文件第一行
- **define.ini 安全**：值不得含换行符或 `=`，写入使用整文件覆写
- **bash 安全**：不执行用户任意 shell 命令，文件名从已验证维度名构造
- **敏感数据**：不脱敏，用户通过 `ignore` 主动排除，`.ustht/` 安全性由用户保障

完整安全规范见 [references/safety.md](references/safety.md)。
