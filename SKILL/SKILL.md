========START_UserThoughts.SKILL/SKILL.md========
# UserThoughts.SKILL

## 重要声明
- **本SKILL**系指本文档(SKILL.md)内容及其同目录下的其他内容,整体称为`UserThoughts.SKILL`,中文名`用户想法.SKILL`
- 本SKILL的**作者**系指GitHub平台用户**JularDepick**
- 本SKILL的**来源**是GitHub仓库`https://github.com/JularDepick/UserThoughts.SKILL`
- **用户**系指使用、调用、提及本SKILL的发言人
- **技能命令**系指在本SKILL内定义的、由`/ustht`引导的命令
- `...`表示...是被标记完整的**单行文本**

---

## 目录定义
以下目录定义适用于本SKILL的影响范围内：
- `@/`指本技能实际安装的位置目录(**SKILL.md所在目录**)
- `~/`指**工作目录**
- `#ustht/`系指目录`~/.ustht/`
- `#mdbase/`系指目录`~/.ustht/mdbase/`
- `#ignored/`系指目录`/.ustht/ignored/`
- `#raw/`系指目录`/.ustht/raw/`
- `#export/`系指目录`/.ustht/export/`

---

## #ustht目录结构
- #ustht/
  - define.ini  # 用于储存工作目录下SKILL状态等宏常量
  - raw/  # 用于存放工作目录下用户原始发言的想法(提取)
    - yyyy-mm-dd.md  # 按日期分片为多个.md文件
  - ignored/  # 用于存放工作目录下用户指定要忽略(不记入mdbase)的原始发言的想法(提取)
    - yyyy-mm-dd.md  # 按日期分片为多个.md文件
  - mdbase/  # 用于存放工作目录下需要整理并记录的用户想法
    - backlog.md  # 用户的计划/待办事项(实时未完成)
    - README.ai.md  # 用于记录mdbase概况、details条目结构/内容概述的文档,便于复用mdbase
    - details/  # 用于存放工作目录下被正式记录的用户原始发言想法
      - rules.md  # 用户对工作目录项目整体的规则性想法
      - plans.md  # 用户对工作目录项目整体的规划性想法
      - ui/  # UI方面的想法
        - outline.md  # UI整体想法
        - details.md  # UI细节想法
      - dev-stack.md  # 用户对工作目录项目整体的技术栈想法
      - ...  # 更多md文件/文件夹
  - export/  # 从mdbase导出的内容

---

## 环境依赖
启用了本SKILL的环境下应该存在以下额外工具(**本SKILL不提供**)：
- `read/write` 读写工具/技能
  - 涉及文件读写时使用
- `bash` 执行命令的工具/技能
  - 涉及文件复制、粘贴、移动、删除时使用
以上工具/技能缺失时需要向用户发出警告

---

## 工具声明

---

## 语法定义
以下语法定义适用于本SKILL.md文档文本内容范围内：
- &[keyname]表示`#ustht/define.ini`文件内keyname键的&引用
- &[keyname]=value表示将`#ustht/define.ini`文件内keyname键对应的值修改为value

---

## 技能命令

命令定义：
- ^$分别匹配用户消息的开头和结尾(忽略前后空白符)
- ^...$是单独成行使用的...命令
- .*...$是附加在正常消息文本末尾的...命令
- [a]表示命令缺失选项时默认使用的选项a
- a|b表示命令对应位置允许两个选项a和b之一
- [a]|b|c表示命令允许三个选项,并且选项缺失时默认使用a选项
- (...)表示对应命令选项是用户自定义的文本/描述

触发规则：
- 当用户发言**完全匹配**某个唯一的命令时
- 当用户发言的**自然语言描述**符合某个唯一命令的操作/执行描述时
- 本SKILL内部要求触发执行某个**技能命令**时

错误处理：

命令列表：
- `^/ustht skill [status]$`
  - 用于检查本技能在**工作目录**下的启用状态
  - 输出&[SKILL_STATUS]的键和值
- `^/ustht skill on|off$`
  - 用于开启/关闭**工作目录**下本技能的启用状态
  - 执行&[SKILL_STATUS]=on|off
- `^/ustht instant [status]$`
  - 用于查看**即时维护**功能启用状态
  - 输出&[INSTANT_STATUS]的键和值
- `^/ustht instant on|off$` 
  - 用于开启/关闭**即时维护**功能启用状态
  - 执行&[INSTANT_STATUS]=on|off
- `^/ustht sortin --[soft]|hard$`
  - 携带选项触发一次 **#ustht维护流程**
- `^/ustht ignore [show]$`
  - 用于查看被忽略记录的想法
  - 逐条列举输出`#ustht/ignored/`目录下的各文件内容
- `^/ustht ignore start|end$`
  - 用于标记上下文中开始/终止忽略用户想法(不记入#mdbase,但仍记入#ignored和#row)
  - 不执行动作,仅做上下文分段标记,便于#ustht维护流程推理
- `^/ustht ignore --last|(...)$` 
  - 忽略上一条用户发言或用户输入的(...)想法(移入#ignore)
- `.*/ustht ignore$`
  - 忽略本条消息的用户想法(放入#ignore)
- ``^/ustht mdbase export --[all]|rules|(...)`
  - 提取导出`#mdbase`目录下的指定文档内容到`#export`目录

命令执行规则：
  - 上下文优先

---

## 即时维护

---

## #ustht维护流程
流程入参：
- 维护力度：`--soft`软维护 | `--hard`硬维护
执行流程：
- 
触发条件：
- 被本SKILL内部计划和设定触发调用时

---

## 第一次使用
当用户在**工作目录下**第一次使用**本SKILL**(即工作目录下**不存在**`.ustht`文件夹)时，**依次执行**以下操作：
- 在`~/`目录下创建`.ustht`文件夹,`#ustht/`目录定义生效
- 尝试把`@/assets/Runtime-Template/`目录下的所有内容**复制**到`#ustht/`目录下
  - 复制失败时向用户报错并跳过SKILL本处定义的后续操作
  - 复制成功时进行下一步操作
- 

---

## 工作模式

---

## 日常决策

---

## mdbase维护方式与触发情况
- 亲自维护
  - 当环境不支持SubAgent子代理时
- 子代理维护
  - 当环境支持SubAgent子代理时

---

## 交互示例

- 用户：
- Agent：

- 用户：
- Agent：

- 用户：这个项目的代码细节要把控好/ustht ignore
- Agent：(这句发言的想法不记入#mdbase,而是记入#ignored)

---
========END_UserThoughts.SKILL/SKILL.md========
