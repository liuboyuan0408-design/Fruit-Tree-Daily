# Karpathy LLM Wiki 双库结构实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在 Fruit-Tree-Daily 仓库与 Obsidian Vault 的 `知识库/` 文件夹中建立 Karpathy LLM Wiki 三文件夹结构(raw/wiki/outputs)及模式文件,形成两套规则一致、互不分叉的通用知识库。

**Architecture:** 纯 Markdown + 文件夹,零工具依赖。每处知识库含:不可变的 `raw/`(原始材料)、AI 维护的 `wiki/`(主题页 + `_index.md` 索引)、按日期归档的 `outputs/`、根目录 `CLAUDE.md` 模式文件(职责/页规范/编译/查询/Lint 规则)、`log.md` 变更日志。

**Tech Stack:** Markdown、文件系统;git(仅仓库侧,一次性提交身份)。

## Global Constraints

- 两处 CLAUDE.md 内容必须完全一致(同一套规则)
- 文件夹名使用英文规范名:`raw` / `wiki` / `outputs`
- 说明语言:中文
- 仓库侧 git 提交身份:一次性参数 `-c user.name="LyxDLiI" -c user.email="LyxDLiI@users.noreply.github.com"`,不改动任何 git 配置文件
- 不改动仓库现有文件(skill.md、fruit-industry-daily-skill/、.gitkeep、docs/superpowers/specs/ 下的设计文档)
- Vault 不是 git 仓库,不做任何 git 操作
- 不迁移 Vault 现有笔记(20260903/、欢迎.md、创建链接.md)

---

### Task 1: 仓库侧目录结构

**Files:**
- Create: `C:\Users\刘博元\Fruit-Tree-Daily\raw\`(含 `.gitkeep`)
- Create: `C:\Users\刘博元\Fruit-Tree-Daily\wiki\`
- Create: `C:\Users\刘博元\Fruit-Tree-Daily\outputs\`(含 `.gitkeep`)
- Create: `C:\Users\刘博元\Fruit-Tree-Daily\outputs\2026-09-21\`

**Interfaces:**
- Consumes: 无(第一任务)
- Produces: 目录 `raw/`、`wiki/`、`outputs/`、`outputs/2026-09-21/`,占位文件 `raw/.gitkeep`、`outputs/.gitkeep`

- [ ] **Step 1: 创建目录与占位文件**

```bash
cd "C:\Users\刘博元\Fruit-Tree-Daily" && mkdir -p raw wiki "outputs/2026-09-21" && touch raw/.gitkeep outputs/.gitkeep
```

- [ ] **Step 2: 验证目录存在且 git 可跟踪**

```bash
ls "C:\Users\刘博元\Fruit-Tree-Daily" && git -C "C:\Users\刘博元\Fruit-Tree-Daily" status --short
```

Expected:`raw`、`wiki`、`outputs` 出现在列表;`git status` 显示 `?? raw/.gitkeep`、`?? outputs/.gitkeep`(wiki/ 为空目录暂不可跟踪,Task 2 写入 _index.md 后即可跟踪)。

- [ ] **Step 3: 提交**

```bash
git -C "C:\Users\刘博元\Fruit-Tree-Daily" -c user.name="LyxDLiI" -c user.email="LyxDLiI@users.noreply.github.com" add raw/.gitkeep outputs/.gitkeep && git -C "C:\Users\刘博元\Fruit-Tree-Daily" -c user.name="LyxDLiI" -c user.email="LyxDLiI@users.noreply.github.com" commit -m "feat: add Karpathy wiki folder structure (raw/wiki/outputs)"
```

Expected: 提交成功,`git log --oneline -1` 显示新提交。

---

### Task 2: 仓库侧模式文件、索引与日志

**Files:**
- Create: `C:\Users\刘博元\Fruit-Tree-Daily\CLAUDE.md`
- Create: `C:\Users\刘博元\Fruit-Tree-Daily\wiki\_index.md`
- Create: `C:\Users\刘博元\Fruit-Tree-Daily\log.md`

**Interfaces:**
- Consumes: Task 1 的目录结构
- Produces: 知识库规则文件 CLAUDE.md、索引 wiki/_index.md、日志 log.md(三者完整内容如下,Task 3 与 Task 4 直接复用/比对)

- [ ] **Step 1: 写入 CLAUDE.md(完整内容,勿删改)**

用 Write 工具创建 `C:\Users\刘博元\Fruit-Tree-Daily\CLAUDE.md`,内容:

````markdown
# 知识库模式文件(Karpathy LLM Wiki)

本目录是基于 Karpathy LLM Wiki 方法论的知识库:纯 Markdown + 文件夹,不用数据库、不用插件。本文件仅约束知识库工作流。

## 三个文件夹的职责

| 文件夹 | 职责 | 规则 |
| --- | --- | --- |
| `raw/` | 原始材料的"倾倒场" | 只进不出:放入后不重命名、不整理、不删除,文件名保留原样 |
| `wiki/` | AI 编译维护的知识页 | 唯一"真相":用户只读不直接编辑,由 AI 在编译步骤中更新 |
| `outputs/` | AI 生成的答案与报告 | 按 `YYYY-MM-DD/` 子文件夹归档;有价值的内容由 AI 沉淀回 `wiki/` |

## wiki 页规范

- 一个主题一页,文件名即主题名(如 `苹果.md`)
- 每页使用固定模板:

```text
# {主题名}

> 一句话摘要:{≤50 字概括本页核心内容}

标签:{标签1}、{标签2}

## 关联主题

- [[相关主题A]]
- [[相关主题B]]

## 正文

{内容}
```

- 新增或修改任何页面后,必须同步更新 `_index.md`
- 页间引用一律使用 `[[wiki-links]]` 双链语法

## 编译工作流(用户说"编译知识库/整理知识库"时执行)

1. 对照 `log.md`,找出 `raw/` 中尚未编译的新材料
2. 读取现有 `wiki/` 全部页面与 `_index.md`,理解已有知识
3. 新材料对应新主题 → 新建 wiki 页;对应已有主题 → 更新该页面
4. 同步更新 `_index.md`(每页一行:主题 → 一句话摘要)
5. 在 `log.md` 末尾追加:`YYYY-MM-DD | 编译 | 新增X页、更新Y页、材料来源`

## 查询工作流(用户提问时执行)

1. 先读 `_index.md` 定位相关主题页,基于 wiki 内容回答
2. wiki 无答案时,先编译 `raw/` 中相关新材料,再回答
3. 回答保存为 `outputs/YYYY-MM-DD/{主题}-问答.md`,注明依据了哪些 wiki 页

## 月度 Lint 规则(每月或用户要求时执行)

- 矛盾论断:不同页面说法冲突 → 保留更新/更权威一方,另一方标注差异
- 无来源论断:无出处的事实 → 补来源,或标注"待核实"
- 过时内容:已失效信息 → 更新,或标注"截至日期"
- 断链:`[[链接]]` 指向不存在的页 → 修正或删除
- 结果记入 `log.md`:`YYYY-MM-DD | lint | 发现X处问题、修复Y处`
````

- [ ] **Step 2: 写入 wiki/_index.md(完整内容,勿删改)**

用 Write 工具创建 `C:\Users\刘博元\Fruit-Tree-Daily\wiki\_index.md`,内容:

```markdown
# 知识库索引

本索引由 AI 在每次编译后同步维护。每行格式:`- [[主题页]] — 一句话摘要`

## 主题页

- (暂无。向 `raw/` 放入材料后,对 AI 说"编译知识库"即可生成主题页。)
```

- [ ] **Step 3: 写入 log.md(完整内容,勿删改)**

用 Write 工具创建 `C:\Users\刘博元\Fruit-Tree-Daily\log.md`,内容:

```markdown
# 变更日志

每次编译或 Lint 后,由 AI 在末尾追加一行。

| 日期 | 操作 | 内容 |
| --- | --- | --- |
| 2026-09-21 | init | 初始化知识库结构(raw / wiki / outputs) |
```

- [ ] **Step 4: 验证内容落盘**

```bash
wc -l "C:\Users\刘博元\Fruit-Tree-Daily\CLAUDE.md" "C:\Users\刘博元\Fruit-Tree-Daily\wiki\_index.md" "C:\Users\刘博元\Fruit-Tree-Daily\log.md" && grep -c "月度 Lint" "C:\Users\刘博元\Fruit-Tree-Daily\CLAUDE.md"
```

Expected: 三个文件行数 > 0;grep 输出 `1`。

- [ ] **Step 5: 提交**

```bash
git -C "C:\Users\刘博元\Fruit-Tree-Daily" -c user.name="LyxDLiI" -c user.email="LyxDLiI@users.noreply.github.com" add CLAUDE.md wiki/_index.md log.md && git -C "C:\Users\刘博元\Fruit-Tree-Daily" -c user.name="LyxDLiI" -c user.email="LyxDLiI@users.noreply.github.com" commit -m "feat: add wiki schema, index, and change log"
```

Expected: 提交成功;`git status --short` 为空(仓库侧全部文件已提交)。

---

### Task 3: Vault 侧结构与文件

**Files:**
- Create: `C:\Users\刘博元\Documents\Obsidian Vault\知识库\raw\`
- Create: `C:\Users\刘博元\Documents\Obsidian Vault\知识库\wiki\`
- Create: `C:\Users\刘博元\Documents\Obsidian Vault\知识库\outputs\2026-09-21\`
- Create: `C:\Users\刘博元\Documents\Obsidian Vault\知识库\CLAUDE.md`(内容与 Task 2 Step 1 完全相同)
- Create: `C:\Users\刘博元\Documents\Obsidian Vault\知识库\wiki\_index.md`(内容与 Task 2 Step 2 完全相同)
- Create: `C:\Users\刘博元\Documents\Obsidian Vault\知识库\log.md`(内容与 Task 2 Step 3 完全相同)

**Interfaces:**
- Consumes: Task 2 定义的三份文件内容(逐字复用)
- Produces: Vault 侧完整知识库结构。Vault 非 git 仓库,无提交步骤。

- [ ] **Step 1: 创建目录**

```bash
mkdir -p "C:\Users\刘博元\Documents\Obsidian Vault\知识库\raw" "C:\Users\刘博元\Documents\Obsidian Vault\知识库\wiki" "C:\Users\刘博元\Documents\Obsidian Vault\知识库\outputs\2026-09-21"
```

- [ ] **Step 2: 写入三个文件**

用 Write 工具分别创建 `知识库\CLAUDE.md`、`知识库\wiki\_index.md`、`知识库\log.md`,内容逐字复制 Task 2 Step 1/2/3 中的内容,不做任何修改。

- [ ] **Step 3: 验证 Vault 侧结构完整**

```bash
find "C:\Users\刘博元\Documents\Obsidian Vault\知识库" -type f -o -type d | sort
```

Expected: 输出包含 `知识库\CLAUDE.md`、`知识库\log.md`、`知识库\raw`、`知识库\wiki`、`知识库\wiki\_index.md`、`知识库\outputs`、`知识库\outputs\2026-09-21`。

---

### Task 4: 双库一致性验收

**Files:**
- 无新建;比对 Task 2 与 Task 3 的产物

**Interfaces:**
- Consumes: Task 2(仓库侧)、Task 3(Vault 侧)的全部产物
- Produces: 验收结论(全部通过才算任务完成)

- [ ] **Step 1: 三份文件逐字比对**

```bash
diff "C:\Users\刘博元\Fruit-Tree-Daily\CLAUDE.md" "C:\Users\刘博元\Documents\Obsidian Vault\知识库\CLAUDE.md" && diff "C:\Users\刘博元\Fruit-Tree-Daily\wiki\_index.md" "C:\Users\刘博元\Documents\Obsidian Vault\知识库\wiki\_index.md" && diff "C:\Users\刘博元\Fruit-Tree-Daily\log.md" "C:\Users\刘博元\Documents\Obsidian Vault\知识库\log.md" && echo "IDENTICAL"
```

Expected: 无 diff 输出,末尾打印 `IDENTICAL`。

- [ ] **Step 2: 仓库侧 git 状态确认**

```bash
git -C "C:\Users\刘博元\Fruit-Tree-Daily" status --short && git -C "C:\Users\刘博元\Fruit-Tree-Daily" log --oneline
```

Expected: `status` 无输出(工作区干净);`log` 至少包含三个提交(设计文档、目录结构、模式文件)。

- [ ] **Step 3: 对照设计文档逐项验收**

逐项核对 `docs/superpowers/specs/2026-09-21-karpathy-wiki-structure-design.md` 的验收标准:

1. 两处目录结构按布局存在 ✓(Task 1/3 验证输出)
2. 两处 CLAUDE.md 内容一致 ✓(Step 1 diff)
3. wiki/_index.md 与 log.md 有初始内容 ✓(Task 2 Step 4、Task 3 Step 3)
4. 仓库侧 raw/、outputs/ 有 .gitkeep,git 可跟踪 ✓(Task 1 Step 2)
5. 仓库侧新增文件已提交 ✓(Step 2 git status 干净)

- [ ] **Step 4: 汇报**

向用户汇报:两处结构清单、仓库侧提交列表、使用方法(丢材料到 raw → 说"编译知识库";提问;每月 Lint)。
