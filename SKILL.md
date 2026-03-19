---
name: openclaw-soul
description: OpenClaw 自我进化框架一键部署。安装宪法(AGENTS.md)、可进化灵魂(SOUL.md)、心跳系统、六层记忆架构、目标管理、思维方法论（HDD/SDD）、跨会话存档（save-game/load-game），并通过场景化对话引导用户定义 Agent 性格。自动配置 EvoClaw（审批制进化）和 Self-Improving Agent（自主学习）。触发场景：(1) 用户说"灵魂框架"、"部署灵魂"、"部署灵魂系统" (2) 用户说"BOOTSTRAP"、"首次对话"、"第一次对话" (3) 用户说"安装进化框架"、"部署进化框架" (4) 用户提到"openclaw-soul"。注意：这是 OpenClaw 部署的第一步，完成后会引导使用 openclaw-setup 配置技术参数。
metadata:
  clawdbot:
    emoji: "🧬"
    requires:
      bins: []
    os: ["linux", "darwin", "win32"]
---

# openclaw-soul — Self-Evolution Framework

为 OpenClaw 安装完整的自我进化框架：宪法 + 可进化灵魂 + 结构化心跳协议 + 六层记忆 + 思维方法论 + 跨会话存档 + 目标管理 + 治理配置。

**安装内容**：
- 9 个工作区文件（AGENTS.md, SOUL.md, HEARTBEAT.md, BOOTSTRAP.md, USER.md, IDENTITY.md, GOALS.md, working-memory.md, long-term-memory.md）
- 7 个依赖 skill（evoclaw, self-improving, hdd, sdd, save-game, load-game, project-skill-pairing）
- 2 个记忆基础设施脚本（merge-daily-transcript.js, auto-commit.sh）
- Heartbeat 定时任务配置
- EvoClaw 治理配置（advisory 模式 + soul-revisions 回滚）
- 六层记忆目录结构 + Git 版本管理
- 向量搜索配置引导
- 引导对话启动（BOOTSTRAP.md）

---

## §1 [GATE] 确认安装环境

**在执行任何操作之前，必须先完成此步骤。跳过此步骤是禁止的。**

询问用户：

> "准备部署 openclaw-soul 自我进化框架。请确认：
> 1. 工作区路径是什么？（默认：`~/.openclaw/workspace/`）
> 2. 如有自定义路径请告知。"

将用户确认的路径记为 `$WORKSPACE`。如果用户说"默认"或不指定，使用 `~/.openclaw/workspace/`。

---

## §2 [GATE] 环境检查

**必须先通过环境检查，否则禁止继续后续步骤。**

运行预检脚本：

```bash
python3 "$(dirname "$0")/../scripts/preflight_check.py"
```

如果脚本不可用，手动执行以下检查：

1. **工作区目录存在**：`$WORKSPACE` 路径必须存在且可写
2. **openclaw.json 可读**：检查 `$WORKSPACE/../openclaw.json` 是否存在且为有效 JSON
3. **clawhub CLI 可用**：`which clawhub` 或检查 `~/.openclaw/bin/clawhub` 或 `/usr/local/bin/clawhub` 或 `/usr/bin/clawhub`
4. **列出已有文件**：检查以下 9 个文件是否已存在于 `$WORKSPACE`：
   - AGENTS.md, SOUL.md, HEARTBEAT.md, BOOTSTRAP.md, GOALS.md
   - USER.md, IDENTITY.md, working-memory.md, long-term-memory.md

**输出格式**：逐项报告 pass/warn/fail，标记需要备份的文件。

**判断逻辑**：
- 任何 `fail` → 报告问题并**停止**，告知用户如何修复
- 只有 `warn` → 告知用户限制，确认是否继续
- 全部 `pass` → 继续下一步

### 2a. clawhub 引导（如果检测为 warn）

**如果 clawhub 不可用**，在继续前告知用户：

> "⚠️ 未检测到 clawhub（OpenClaw 技能包管理器）。
>
> clawhub 可以让你安装完整版的 EvoClaw 和 Self-Improving Agent，支持独立管理和更新。
>
> **安装方法**：
> ```bash
> npm install -g @openclaw/clawhub
> # 或者
> pnpm add -g @openclaw/clawhub
> ```
>
> **你可以选择**：
> 1. 现在安装 clawhub（推荐）→ 安装后重新触发 openclaw-soul
> 2. 继续部署 → 使用内联版本（功能完整，只是管理方式不同）
>
> 选择 2 不影响使用，核心的自我进化机制已经内置在 AGENTS.md 中。"

如果用户选择 1，暂停部署并告知重新触发方式。
如果用户选择 2，继续 §3。

---

## §3 [REQUIRED] 备份已有文件

对每个即将部署的文件，如果已存在于 `$WORKSPACE`：

```bash
cp "$WORKSPACE/{filename}" "$WORKSPACE/{filename}.backup.$(date +%Y%m%d-%H%M%S)"
```

列出所有被备份的文件告知用户。如果没有需要备份的文件，跳过此步骤并告知用户。

**保证非破坏性**：原文件在备份完成后才会被覆盖。

---

## §4 [REQUIRED] 模板部署

从 `references/` 目录读取模板，写入 `$WORKSPACE`：

| 模板文件 | 目标位置 |
|---------|---------|
| `references/agents-template.md` | `$WORKSPACE/AGENTS.md` |
| `references/soul-template.md` | `$WORKSPACE/SOUL.md` |
| `references/heartbeat-template.md` | `$WORKSPACE/HEARTBEAT.md` |
| `references/bootstrap-guide.md` | `$WORKSPACE/BOOTSTRAP.md` |
| `references/user-template.md` | `$WORKSPACE/USER.md` |
| `references/identity-template.md` | `$WORKSPACE/IDENTITY.md` |
| `references/goals-template.md` | `$WORKSPACE/GOALS.md` |
| `references/working-memory-template.md` | `$WORKSPACE/working-memory.md` |
| `references/long-term-memory-template.md` | `$WORKSPACE/long-term-memory.md` |

**部署步骤**：
1. 读取 `references/` 中的模板文件内容
2. 写入对应的 `$WORKSPACE` 目标路径
3. 创建目录结构（如不存在）：
   ```
   $WORKSPACE/
   ├── memory/
   │   ├── daily/              # Layer 2: daily notes
   │   ├── entities/           # Layer 3: knowledge graph
   │   ├── transcripts/        # Layer 5: full dialogue archive
   │   ├── projects/           # Layer 6: project-scoped memory
   │   ├── voice/              # Voice JSONL (optional)
   │   ├── experiences/        # EvoClaw
   │   ├── significant/        # EvoClaw
   │   ├── reflections/        # EvoClaw
   │   ├── proposals/          # EvoClaw
   │   └── pipeline/           # EvoClaw
   ├── scripts/                # Memory infrastructure scripts
   └── soul-revisions/         # SOUL.md version snapshots
   ```
4. 部署记忆基础设施脚本：
   - 从 `fallback/memory-deposit/scripts/merge-daily-transcript.js` 复制到 `$WORKSPACE/scripts/`
   - 从 `fallback/memory-deposit/scripts/auto-commit.sh` 复制到 `$WORKSPACE/scripts/`
   - 验证两个脚本文件存在且非空
5. 每个文件写入后读回验证——确认文件非空且内容完整

如果任何文件写入失败，立即停止并报告错误。

---

## §5 [REQUIRED] 依赖 Skill 安装（三级 Fallback）

### 5a. 检查 clawhub 可用性

```bash
which clawhub || test -f ~/.openclaw/bin/clawhub
CLAWHUB_AVAILABLE=$?
```

记录 clawhub 是否可用。

### 5b. 安装 EvoClaw

检查 `$WORKSPACE/skills/evoclaw/SKILL.md` 是否存在：

**已安装** → 跳过，告知用户

**未安装** → 按优先级尝试：

1. **Level 1 - clawhub 安装**（如果可用）
   ```bash
   clawhub install evoclaw --force
   ```
   如果成功，告知用户已从 clawhub 安装。

2. **Level 2 - 离线 Fallback**（如果 Level 1 失败或 clawhub 不可用）
   - 检查本 skill 所在目录的 `fallback/evoclaw/SKILL.md` 是否存在
   - 如果存在：
     ```bash
     cp -r "$(dirname "$0")/../fallback/evoclaw" "$WORKSPACE/skills/"
     ```
   - 如果 fallback 文件也不存在，记录警告继续下一步

3. **Level 3 - AGENTS.md 内联版本**
   > "ℹ️ 使用 AGENTS.md 内联版本。
   >
   > **功能对标**：
   > - EvoClaw: 核心 Identity 变更需要用户批准 ✓
   > - Self-Improving: 用户纠正自动学习 ✓
   > - SOUL.md 变更前自动创建快照 ✓
   >
   > **与完整版的区别**：
   > - 内联版：规则写在 AGENTS.md，随 workspace 同步
   > - 完整版：独立 skill，可通过 clawhub 更新和管理
   >
   > 💡 **想升级到完整版？** 安装 clawhub 后执行：
   > ```bash
   > clawhub install evoclaw
   > clawhub install self-improving
   > ```"

### 5c. 安装 Self-Improving Agent

检查 `$WORKSPACE/skills/self-improving/SKILL.md` 是否存在：

**已安装** → 跳过，告知用户

**未安装** → 按优先级尝试：

1. **Level 1 - clawhub 安装**（如果可用）
   ```bash
   clawhub install self-improving --force
   ```
   如果成功，告知用户已从 clawhub 安装。

2. **Level 2 - 离线 Fallback**（如果 Level 1 失败或 clawhub 不可用）
   - 检查本 skill 所在目录的 `fallback/self-improving/SKILL.md` 是否存在
   - 如果存在：
     ```bash
     cp -r "$(dirname "$0")/../fallback/self-improving" "$WORKSPACE/skills/"
     ```
   - 如果 fallback 文件也不存在，记录警告继续下一步

3. **Level 3 - AGENTS.md 内联版本**
   > "ℹ️ 使用 AGENTS.md 内联版本。
   >
   > **功能对标**：
   > - Self-Improving: 用户纠正自动学习 ✓
   > - 规则持久化和优先级管理 ✓
   > - 30天未使用自动归档 ✓
   >
   > **与完整版的区别**：
   > - 内联版：规则写在 AGENTS.md，随 workspace 同步
   > - 完整版：独立 skill，可通过 clawhub 更新和管理
   >
   > 💡 **想升级到完整版？** 安装 clawhub 后执行：
   > ```bash
   > clawhub install self-improving
   > ```"

### 5d. 安装思维方法论与项目管理 Skills

以下 5 个 skill 使用同样的三级 Fallback 机制安装：

| Skill | clawhub 名称 | Fallback 路径 | 功能 |
|-------|-------------|-------------|------|
| HDD | `hdd` | `fallback/hdd/` | 假设驱动开发 |
| SDD | `sdd` | `fallback/sdd/` | 场景驱动开发 |
| save-game | `save-game` | `fallback/save-game/` | 项目存档 |
| load-game | `load-game` | `fallback/load-game/` | 项目恢复 |
| project-skill-pairing | `project-skill-pairing` | `fallback/project-skill-pairing/` | 项目与 Skill 结对 |

对每个 skill，按顺序尝试：
1. **Level 1**: `clawhub install <name> --force`（如果可用）
2. **Level 2**: `cp -r "$(dirname "$0")/../fallback/<name>" "$WORKSPACE/skills/"`
3. **Level 3**: 记录警告，继续下一个（这些 skill 没有 AGENTS.md 内联版本——它们是独立方法论，必须以 skill 形式存在）

### 5e. 验证结果

安装完成后，逐项报告：

```
✓ EvoClaw: [installed from clawhub | installed from fallback | using inline version]
✓ Self-Improving: [installed from clawhub | installed from fallback | using inline version]
✓ HDD: [installed from clawhub | installed from fallback | ⚠️ not installed]
✓ SDD: [installed from clawhub | installed from fallback | ⚠️ not installed]
✓ save-game: [installed from clawhub | installed from fallback | ⚠️ not installed]
✓ load-game: [installed from clawhub | installed from fallback | ⚠️ not installed]
✓ project-skill-pairing: [installed from clawhub | installed from fallback | ⚠️ not installed]
```

- EvoClaw 和 Self-Improving "using inline version" 是正常的
- HDD/SDD/save-game/load-game/project-skill-pairing 如果未安装，提示用户手动从 fallback 复制
- 如果有 fallback 或 Level 1 安装，验证对应的 SKILL.md 文件存在且非空

---

## §6 [REQUIRED] EvoClaw 治理配置

### 6a. 治理配置

写入 `$WORKSPACE/memory/evoclaw-state.json`：

```json
{
  "mode": "advisory",
  "require_approval": ["Core Identity", "Capability Tree", "Value Function"],
  "auto_sections": ["Working Style", "User Understanding", "Evolution Log"],
  "revision_dir": "soul-revisions",
  "initialized": true
}
```

### 6b. 验证

确认 evoclaw-state.json 存在且可解析为有效 JSON。

---

## §7 [REQUIRED] Self-Improving Agent 初始化

### 7a. 目录结构

确保以下目录存在：
```
~/self-improving/
├── projects/
├── domains/
└── archive/
```

### 7b. 初始化文件

如果以下文件不存在，创建初始版本：

**~/self-improving/memory.md**:
```markdown
# Self-Improving Memory

> Execution patterns and learned rules. Updated after corrections and lessons.

(empty — will accumulate through use)
```

**~/self-improving/corrections.md**:
```markdown
# Corrections Log

> Failed attempts and their fixes. Each entry prevents the same mistake twice.

(empty — will accumulate through use)
```

**~/self-improving/index.md**:
```markdown
# Self-Improving Index

> Quick reference to all domains, projects, and archived knowledge.

## Domains
(none yet)

## Projects
(none yet)

## Archive
(none yet)
```

如果文件已存在，**不要覆盖**——它们可能包含已积累的学习内容。

---

## §8 [REQUIRED] Heartbeat 配置

使用 `openclaw config set` 更新 heartbeat 设置：

```bash
openclaw config set agents.defaults.heartbeat.every "1h"
openclaw config set agents.defaults.heartbeat.target "last"
openclaw config set agents.defaults.heartbeat.directPolicy "allow"
```

如果 `openclaw config set` 命令不可用，直接编辑 `openclaw.json`：

1. 读取当前 openclaw.json
2. 确保 `agents.defaults.heartbeat` 对象存在
3. 设置：
   - `every`: `"1h"`
   - `target`: `"last"`
   - `directPolicy`: `"allow"`
4. 写回文件

---

## §8.5 [REQUIRED] 向量搜索配置

**没有向量搜索的记忆系统是摆设——写了等于白写。此步骤不可跳过。**

### 8.5a. 检测当前状态

执行 `memory_search(query="test memory recall")`：

- **有结果** → embedding 已就绪，跳到 8.5b
- **报错或无结果** → 需要配置 embedding provider

### 8.5b. 配置 embedding provider（如果未就绪）

向用户说明并推荐方案：

> "向量搜索需要一个 embedding API key，这是记忆系统的核心能力——没有它，我就无法从历史记忆中检索信息。"

| 方案 | 模型 | 价格 | 说明 |
|------|------|------|------|
| **Gemini** | `gemini-embedding-001` | 免费额度大 | 配置最简单 |
| **硅基流动 SiliconFlow** | `BAAI/bge-large-zh-v1.5` 或 `BAAI/bge-m3` | 免费（注册送额度） | 中文效果好，OpenAI 兼容接口 |
| **OpenAI** | `text-embedding-3-small` | 付费 | 效果稳定 |

硅基流动配置示例（provider 设为 openai，用自定义 baseUrl）：
```json5
memorySearch: {
  provider: "openai",
  model: "BAAI/bge-large-zh-v1.5",
  remote: {
    baseUrl: "https://api.siliconflow.cn/v1",
    apiKey: "<用户的 SiliconFlow API Key>"
  }
}
```

确认用户选定方案后，用 `gateway(action=config.patch)` 或直接编辑 `openclaw.json` 配好。

### 8.5c. 配置 extraPaths

确保所有重要目录纳入索引：

```json5
memorySearch: {
  extraPaths: ["memory/transcripts", "memory/projects", "AGENTS.md"]
}
```

### 8.5d. 开启高级搜索功能

```json5
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "query": {
          "hybrid": {
            "mmr": { "enabled": true, "lambda": 0.7 },
            "temporalDecay": { "enabled": true, "halfLifeDays": 30 }
          }
        },
        "cache": { "enabled": true, "maxEntries": 50000 }
      }
    }
  }
}
```

### 8.5e. 验证

再次执行 `memory_search(query="test")` 确认可用。如果 memory/ 下还没有文件，告知用户："向量搜索已就绪，等积累了笔记后就能搜到了。"

---

## §8.6 [REQUIRED] Git 版本管理初始化

### 8.6a. 初始化 Git

检查 `$WORKSPACE/.git/` 是否存在：

**不存在** →
```bash
cd $WORKSPACE && git init
```

写入 `.gitignore`（排除敏感文件和临时文件）：
```
.env*
*.secrets
credentials.json
tmp/
node_modules/
.DS_Store
```

执行首次提交：
```bash
git add -A && git commit -m "init: openclaw-soul workspace"
```

**已存在** → 检查 `.gitignore` 包含上述排除项，缺的补上。

### 8.6b. 验证

确认 `git status` 可正常执行。

---

## §9 [REQUIRED] 验证清单

逐项检查并报告 pass/fail：

| # | 检查项 | 验证方法 |
|---|--------|---------|
| 1 | AGENTS.md 存在且非空 | `test -s $WORKSPACE/AGENTS.md` |
| 2 | SOUL.md 存在且非空 | `test -s $WORKSPACE/SOUL.md` |
| 3 | HEARTBEAT.md 存在且非空 | `test -s $WORKSPACE/HEARTBEAT.md` |
| 4 | BOOTSTRAP.md 存在且非空 | `test -s $WORKSPACE/BOOTSTRAP.md` |
| 5 | GOALS.md 存在且非空 | `test -s $WORKSPACE/GOALS.md` |
| 6 | USER.md 存在且非空 | `test -s $WORKSPACE/USER.md` |
| 7 | IDENTITY.md 存在且非空 | `test -s $WORKSPACE/IDENTITY.md` |
| 8 | working-memory.md 存在且非空 | `test -s $WORKSPACE/working-memory.md` |
| 9 | long-term-memory.md 存在且非空 | `test -s $WORKSPACE/long-term-memory.md` |
| 10 | EvoClaw skill 已安装 | `test -f $WORKSPACE/skills/evoclaw/SKILL.md` |
| 11 | evoclaw-state.json 配置正确 | 读取并验证 mode=advisory |
| 12 | Self-Improving skill 已安装 | `test -f $WORKSPACE/skills/self-improving/SKILL.md` |
| 13 | ~/self-improving/ 目录就绪 | 检查 memory.md, corrections.md, index.md |
| 14 | Heartbeat 配置已写入 | 读取 openclaw.json 确认 heartbeat 字段 |
| 15 | memory/entities/ 目录存在 | `test -d $WORKSPACE/memory/entities` |
| 16 | memory/daily/ 目录存在 | `test -d $WORKSPACE/memory/daily` |
| 17 | soul-revisions/ 目录存在 | `test -d $WORKSPACE/soul-revisions` |
| 18 | memory/transcripts/ 目录存在 | `test -d $WORKSPACE/memory/transcripts` |
| 19 | memory/projects/ 目录存在 | `test -d $WORKSPACE/memory/projects` |
| 20 | scripts/merge-daily-transcript.js 存在 | `test -f $WORKSPACE/scripts/merge-daily-transcript.js` |
| 21 | scripts/auto-commit.sh 存在 | `test -f $WORKSPACE/scripts/auto-commit.sh` |
| 22 | HDD skill 已安装 | `test -f $WORKSPACE/skills/hdd/SKILL.md` |
| 23 | SDD skill 已安装 | `test -f $WORKSPACE/skills/sdd/SKILL.md` |
| 24 | save-game skill 已安装 | `test -f $WORKSPACE/skills/save-game/SKILL.md` |
| 25 | load-game skill 已安装 | `test -f $WORKSPACE/skills/load-game/SKILL.md` |
| 26 | project-skill-pairing skill 已安装 | `test -f $WORKSPACE/skills/project-skill-pairing/SKILL.md` |
| 27 | 向量搜索可用 | `memory_search(query="test")` 不报错 |
| 28 | Git 已初始化 | `test -d $WORKSPACE/.git` |

**输出格式**：

```
✓ AGENTS.md — pass
✓ SOUL.md — pass
✓ GOALS.md — pass
...
✗ EvoClaw — fail (SKILL.md not found)
```

**判断**：
- 全部 pass → 继续 §10
- 任何 fail → 列出失败项，提示用户手动修复

---

## §10 [FINAL] 触发引导对话

**只有 §9 全部通过后才执行此步骤。**

1. 读取 `$WORKSPACE/BOOTSTRAP.md`
2. 告知用户：

> "🧬 openclaw-soul 部署完成！
>
> 已安装：
> - 宪法（AGENTS.md）— 含 Conductor 协议、委派规范、搜索协议、Skill 路由
> - 可进化灵魂（SOUL.md）— 带版本快照回滚 + 思维方法论自我认知
> - 结构化心跳协议（HEARTBEAT.md）— 唤醒上下文 + 阻塞去重 + 预算感知 + 对话合并 + Git 自动提交
> - 六层记忆系统（working-memory → daily → entities → tacit → transcripts → projects）
> - 记忆基础设施（向量搜索 + Git 版本管理 + 对话合并脚本）
> - 思维方法论（HDD 假设驱动 + SDD 场景驱动）
> - 跨会话连续性（save-game 存档 + load-game 恢复 + HANDOFF.md 交接）
> - 项目管理（project-skill-pairing 三层分级）
> - 目标管理（GOALS.md）— 任务追溯到目标
> - EvoClaw 治理（advisory 模式）
> - Self-Improving Agent
>
> **下一步：首次深度对话（BOOTSTRAP）**
>
> 接下来我们要进行一次 10-15 分钟的深度对话，分三个阶段：
> 1. **认识你** — 了解你是谁、在做什么、需要什么帮助
> 2. **定义我的性格** — 通过场景化问答，让你选择我的沟通风格
> 3. **确认身份** — 给我起个名字，确认我的核心定位
>
> 这次对话会塑造我的灵魂（SOUL.md），之后我就能以你期望的方式工作。
>
> 💡 **触发词提示**：以后如果需要重新触发这个流程，可以说"首次对话"、"BOOTSTRAP"或"灵魂框架"。
>
> 准备好了吗？我们开始吧。"

3. **立即开始执行 BOOTSTRAP.md 的 Phase 1**——从这里开始就是 BOOTSTRAP 的流程接管
4. 此 skill 的使命到此结束。后续由 BOOTSTRAP.md + AGENTS.md + SOUL.md 接管运行

---

_openclaw-soul v2.0.0 — Six-layer memory, thinking methodology, cross-session continuity. Give your AI a soul that grows._
