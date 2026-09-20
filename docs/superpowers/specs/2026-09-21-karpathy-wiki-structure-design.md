# 设计文档:Karpathy LLM Wiki 双库结构

- 日期:2026-09-21
- 状态:已确认(用户于 2026-09-21 批准)
- 方法论依据:Karpathy LLM Wiki(raw → wiki → outputs 三文件夹 + 根目录模式文件)

## 背景

用户要求基于 Karpathy wiki 方法论,为两个位置建立文件夹结构:

1. **仓库侧**:`C:\Users\刘博元\Fruit-Tree-Daily`(已克隆的 GitHub 仓库)
2. **Vault 侧**:`C:\Users\刘博元\Documents\Obsidian Vault\知识库\`(独立顶层文件夹)

用途定位:**通用知识库**(不限果业)。两处采用同一套结构与管理规则,避免知识分叉。

## 结构设计(方案 B:核心 + 轻量扩展)

### 仓库侧

在保留现有文件(skill.md、fruit-industry-daily-skill/、.gitkeep)基础上新增:

```
Fruit-Tree-Daily/
├── CLAUDE.md          # 模式文件(新增)
├── raw/               # 原始材料(新增,含 .gitkeep 占位)
├── wiki/
│   └── _index.md      # 主题索引(新增)
├── outputs/
│   └── 2026-09-21/    # 按日期归档(新增,含 .gitkeep 占位)
└── log.md             # 编译变更日志(新增)
```

### Vault 侧

```
Obsidian Vault/
└── 知识库/
    ├── CLAUDE.md      # 模式文件(与仓库侧同一套规则)
    ├── raw/
    ├── wiki/
    │   └── _index.md
    ├── outputs/
    │   └── 2026-09-21/
    └── log.md
```

## 模式文件(CLAUDE.md)内容要点

- **职责定义**:raw 只进不出、不做整理;wiki 是唯一"真相",用户只读不直接改;outputs 沉淀成果并可反哺 wiki
- **wiki 页规范**:一个主题一页;固定页面模板(标题 / 一句话摘要 / 标签 / 关联主题 [[双链]] / 正文);新增或修改页面必须同步更新 `_index.md`
- **编译工作流**:AI 读 raw 新增材料 + 现有 wiki → 更新/新建主题页 → 同步索引 → 在 log.md 追加一行变更记录
- **查询工作流**:用户提问 → AI 基于 wiki 回答 → 答案写入 `outputs/YYYY-MM-DD/`
- **月度 Lint 规则**:检查矛盾论断、无来源论断、过时内容、断链
- 说明语言:中文;文件夹名保留英文规范名(raw/wiki/outputs)

## 范围边界(YAGNI)

- 不建 atoms 原子层、主题分支文件夹、Lint 脚本(方案 C 内容,规模需要时再加)
- 不改动 fruit-industry-daily 技能本身
- 不迁移用户现有笔记(20260903/ 等)

## 验收标准

1. 两处目录结构按上述布局存在
2. 两处 CLAUDE.md 内容一致(允许路径表述差异),覆盖职责、页规范、编译/查询工作流、Lint 规则
3. wiki/_index.md 与 log.md 有初始内容
4. 仓库侧空文件夹(raw/、outputs/)有 .gitkeep 占位,git status 可跟踪
5. 仓库侧新增文件已提交 git
