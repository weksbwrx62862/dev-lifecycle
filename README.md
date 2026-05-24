<h1 align="center">Dev Lifecycle</h1>

<p align="center"><strong>软件开发生命周期技能包 v2 — 从需求澄清到交付交接的全链路工作流引擎</strong></p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-blue.svg" alt="Python">
  <img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License">
  <img src="https://img.shields.io/badge/Hermes-%3E%3D2.0.0-orange.svg" alt="Hermes Compat">
  <img src="https://img.shields.io/badge/version-2.0.0-blue.svg" alt="Version">
  <img src="https://img.shields.io/github/last-commit/weksbwrx62862/dev-lifecycle.svg" alt="Last Commit">
</p>

---

Dev Lifecycle 是 [Hermes Agent](https://github.com/weksbwrx62862/hermes) 框架的后端技能包，将软件开发生命周期建模为三阶段状态机（构思 → 构建 → 交付），覆盖 21+ 技能的导航、质量门禁、项目上下文感知与遥测统计。v2 新增工作流持久化、阶段准入检查和技能推荐引擎，让 AI Agent 在任意项目中都能按规范推进开发流程。

## 功能矩阵

| 能力 | 描述 | v2 新增 |
|------|------|---------|
| 三阶段工作流 | ideate → build → deliver，21+ 技能自动映射 | |
| 工作流状态机 | start / advance / rollback / resume / report 完整生命周期 | ✅ |
| 质量门禁 | 阶段间自动检查准入条件（PRD、测试、审查等） | ✅ |
| 项目上下文感知 | 自动探测语言、框架、测试结构，调整技能推荐顺序 | ✅ |
| 遥测统计 | 技能使用频次、阶段耗时、跳过率、低效技能检测 | ✅ |
| 配置管理 | lifecycle.yaml 自定义阶段与技能，与默认配置合并 | ✅ |
| 会话恢复 | on_session_start 钩子自动检测未完成工作流并注入提示 | ✅ |

## 架构图

```
                        Dev Lifecycle v2 架构

 ┌─────────────────────────────────────────────────────────────────────┐
 │                        Hermes Agent Framework                       │
 │                                                                     │
 │  on_session_start ──► handle_on_session_start()                    │
 │       │                    │                                        │
 │       │                    ├─ 检测未完成工作流                       │
 │       │                    └─ 注入生命周期上下文提示                 │
 │                                                                     │
 │  dev_workflow 工具 ──► handle_dev_workflow(args)                    │
 │       │                    │                                        │
 │       │         ┌──────────┼──────────┐                             │
 │       ▼         ▼          ▼          ▼                             │
 │   overview    stage      skill     workflow                         │
 │                                     │                               │
 │                         ┌───────────┼───────────┐                   │
 │                         ▼           ▼           ▼                   │
 │                      start      advance     rollback               │
 │                         │           │           │                   │
 │                         ▼           ▼           ▼                   │
 │                    ┌─────────────────────────────────┐              │
 │                    │       WorkflowManager            │              │
 │                    │  (SQLite 持久化 · 线程安全)      │              │
 │                    └─────────────────────────────────┘              │
 │                         │           │                               │
 │            ┌────────────┤           ├────────────┐                  │
 │            ▼            ▼           ▼            ▼                  │
 │   ┌──────────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────┐     │
 │   │ QualityGate  │ │ Project  │ │  Skill   │ │  Telemetry   │     │
 │   │  Manager     │ │ Detector │ │Recommender│ │  Recorder   │     │
 │   └──────────────┘ └──────────┘ └──────────┘ └──────────────┘     │
 └─────────────────────────────────────────────────────────────────────┘

 工作流管线:

  💡 ideate (构思)              🔨 build (构建)              🚀 deliver (交付)
 ┌────────────────────┐    ┌────────────────────┐    ┌────────────────────┐
 │ grill-me           │    │ prototype          │    │ systematic-debugging│
 │ grill-with-docs    │    │ spike              │    │ python-debugpy     │
 │ to-prd             │    │ improve-codebase-  │    │ node-inspect-      │
 │ to-issues          │    │   architecture     │    │   debugger         │
 │ plan               │    │ zoom-out           │    │ requesting-code-   │
 │ writing-plans      │    │ test-driven-       │    │   review           │
 │                    │    │   development      │    │ triage             │
 │                    │    │ subagent-driven-   │    │ handoff            │
 │                    │    │   development      │    │                    │
 │                    │    │ user-auth-system   │    │                    │
 └────────┬───────────┘    └────────┬───────────┘    └────────────────────┘
          │                         │
          ▼                         ▼
   ┌──────────────┐          ┌──────────────┐
   │  Gate Check  │          │  Gate Check  │
   │  ✓ PRD 存在  │          │  ✓ 测试通过  │
   │  ✓ Issue 拆解│          │  ✓ 审查完成  │
   │  ✓ 计划编写  │          │  ✓ 无阻塞Bug │
   └──────────────┘          └──────────────┘
```

## 快速开始

### 前置条件

- Python 3.10+
- [Hermes Agent](https://github.com/weksbwrx62862/hermes) >= 2.0.0
- PyYAML (`pip install pyyaml`)

### 安装

```bash
git clone https://github.com/weksbwrx62862/dev-lifecycle.git
cd dev-lifecycle
pip install -e .
```

### 最小示例

在 Hermes 配置中启用插件：

```yaml
# hermes_config.yaml
plugins:
  - name: dev-lifecycle
    path: ./dev-lifecycle
```

启动一个新项目的工作流：

```python
result = dev_workflow(action="start", project_path="/path/to/my-project")
# → 返回项目上下文、当前阶段 (ideate) 和推荐技能列表
```

推进技能完成：

```python
result = dev_workflow(action="advance", skill_name="to-prd")
# → 返回下一技能、阶段推进状态和质量门禁结果
```

## 核心功能详解

### 工作流状态机

工作流状态机基于 SQLite 持久化，线程安全，支持完整的生命周期操作：

| 操作 | 说明 | 必填参数 |
|------|------|----------|
| `start` | 启动新工作流，自动探测项目上下文 | `project_path` |
| `advance` | 标记技能完成，自动判断阶段推进 | `skill_name` |
| `rollback` | 回退到更早阶段，已完成技能标记为 skipped | `to_stage` |
| `resume` | 恢复未完成的工作流 | `project_path` |
| `report` | 生成遥测使用报告 | — |

```python
dev_workflow(action="start", project_path="/home/user/my-api")
dev_workflow(action="advance", skill_name="test-driven-development")
dev_workflow(action="rollback", to_stage="ideate")
dev_workflow(action="resume", project_path="/home/user/my-api")
dev_workflow(action="report")
```

### 质量门禁

阶段间自动执行准入检查，不满足条件则阻断推进并返回修复建议：

**ideate → build 门禁：**

| 检查项 | 条件 | 失败提示 |
|--------|------|----------|
| `prd_exists` | PRD 文档已创建 | PRD 文档缺失，请先完成 PRD 编写 |
| `issues_split` | Issue 数量 > 0 | Issue 尚未拆解，请将需求拆分为可执行的 Issue |
| `plan_written` | 实现计划已编写 | 实现计划缺失，请先编写实现计划 |

**build → deliver 门禁：**

| 检查项 | 条件 | 失败提示 |
|--------|------|----------|
| `tests_passed` | 测试全部通过 | 测试未通过，请修复失败的测试用例 |
| `review_completed` | 代码审查已完成 | 代码审查未完成，请提交代码审查 |
| `no_blocking_bugs` | 阻塞 Bug = 0 | 存在 N 个阻塞 Bug，请优先修复 |

可通过 `QualityGateManager.register_gate()` 注册自定义门禁：

```python
from gates import QualityGateManager, GateCheck

gate_mgr = QualityGateManager()

gate_mgr.register_gate("ideate", "build", GateCheck(
    name="design_doc_exists",
    check_fn=lambda ctx: (bool(ctx.get("design_doc_path")), "设计文档缺失"),
    description="检查设计文档是否存在",
))
```

### 项目上下文感知

`ProjectDetector` 自动探测项目特征，`SkillRecommender` 根据项目类型调整技能推荐顺序：

| 探测维度 | 识别方式 |
|----------|----------|
| 语言 | 标识文件（`pyproject.toml` → Python, `package.json` → Node, `go.mod` → Go, `Cargo.toml` → Rust） |
| 框架 | 文件模式匹配（Django/Flask/FastAPI/React/Vue/Express） |
| 测试 | 目录/配置文件（`tests/`, `pytest.ini`, `jest.config.js` 等） |

推荐规则：
- Python 项目 + build/deliver 阶段 → `python-debugpy` 优先
- Node 项目 + build 阶段 → `node-inspect-debugger` 优先

```python
from context import ProjectDetector, SkillRecommender

detector = ProjectDetector()
ctx = detector.detect("/path/to/project")
# → ProjectContext(languages=['python'], frameworks=['fastapi'], has_tests=True, project_type='python')

recommender = SkillRecommender()
recommended = recommender.recommend(ctx, "build", available_skills)
```

### 遥测统计

`TelemetryRecorder` 记录每次技能使用事件，生成统计报告：

| 统计维度 | 说明 |
|----------|------|
| `skill_usage` | 各技能使用次数 |
| `stage_durations` | 各阶段平均停留时间 |
| `skip_rate` | 各技能跳过率（duration 为 NULL 视为跳过） |
| `top_skills` | 使用最多的前 5 个技能 |
| 低效检测 | 使用 ≤1 次 + 跳过率 > 50% 的技能自动标记，可对接 self-evolution 优化 |

```python
from telemetry import TelemetryRecorder, TelemetryEvent

recorder = TelemetryRecorder()
recorder.record(TelemetryEvent(
    skill_name="test-driven-development",
    stage="build",
    project_type="python",
    duration=120.5,
))

report = recorder.report()
stats = recorder.get_skill_stats("test-driven-development")
```

### 配置管理

支持通过 `~/.hermes/plugins/dev-lifecycle/lifecycle.yaml` 自定义配置，与默认配置合并（自定义优先，默认补充）：

```yaml
# lifecycle.yaml
stages:
  ideate:
    flow:
      - ["grill-me", "无文档需求深挖"]
      - ["to-prd", "对话转 PRD 文档"]
      - ["custom-skill", "自定义技能"]
  build:
    flow:
      - ["prototype", "抛弃式原型验证设计"]

gates:
  ideate_to_build:
    - name: prd_exists
      description: "检查 PRD 文档是否存在"

custom_skills:
  ideate:
    - ["custom-skill", "自定义技能描述"]
```

## 技术栈

```
+-------------------+---------------------------+
| 类别              | 技术                      |
+-------------------+---------------------------+
| 语言              | Python 3.10+              |
| 框架              | Hermes Agent >= 2.0.0     |
| 持久化            | SQLite3 (内置)            |
| 配置              | PyYAML                    |
| 并发安全          | threading.Lock            |
| 数据模型          | dataclasses               |
| 插件声明          | plugin.yaml               |
+-------------------+---------------------------+
```

## 项目结构

```
dev-lifecycle/
├── plugin.yaml          # 插件声明（名称、版本、工具、钩子）
├── __init__.py          # 插件注册入口
├── constants.py         # 生命周期阶段与技能常量
├── state.py             # 工作流状态机（SQLite 持久化）
├── gates.py             # 质量门禁管理器
├── context.py           # 项目上下文探测与技能推荐
├── telemetry.py         # 遥测记录与统计报告
├── config.py            # 配置加载与合并
├── schemas.py           # dev_workflow 工具 Schema 定义
├── handlers.py          # 工具处理器（主入口 + 8 个 action 分发）
└── tests/
    ├── conftest.py      # 测试 fixtures
    ├── test_config.py
    ├── test_context.py
    ├── test_gates.py
    ├── test_handlers.py
    ├── test_helpers.py
    ├── test_schemas.py
    ├── test_state.py
    └── test_telemetry.py
```

## 开发指南

```bash
git clone https://github.com/weksbwrx62862/dev-lifecycle.git
cd dev-lifecycle
pip install -e .
pytest tests/ -v
```

添加自定义质量门禁：在 `gates.py` 中定义检查函数并注册到 `_register_builtin_gates()`，或通过 `lifecycle.yaml` 配置。

添加新阶段技能：在 `constants.py` 的 `LIFECYCLE` 字典中扩展 `flow` 列表，格式为 `("skill-name", "技能用途描述")`。

## 路线图

- [ ] 阶段并行执行支持（build 阶段多技能并行）
- [ ] Web UI 工作流可视化面板
- [ ] 自定义阶段扩展（允许用户定义第四、第五阶段）
- [ ] 遥测数据导出（JSON / CSV）
- [ ] 与 GitNexus 知识图谱集成（影响范围分析驱动门禁）
- [ ] 工作流模板系统（预设不同项目类型的工作流模板）

## 常见问题

**Q: 工作流数据存储在哪里？**

A: 所有工作流状态和遥测数据存储在 `~/.hermes/plugins/dev-lifecycle/workflow.db`（SQLite），无需额外数据库服务。

**Q: 如何自定义阶段和技能？**

A: 创建 `~/.hermes/plugins/dev-lifecycle/lifecycle.yaml`，按配置管理章节的格式定义自定义内容，插件启动时会自动合并。

**Q: 质量门禁检查失败后会怎样？**

A: 门禁失败时 `advance` 操作仍会标记技能完成，但返回的 `gate_check` 字段会包含失败项和修复建议。阶段推进会被阻断，需要满足所有门禁条件后才能继续。

**Q: 支持哪些项目类型？**

A: 目前支持 Python、Node.js、Go、Rust 四种语言的项目探测，以及 Django、Flask、FastAPI、React、Vue、Express 六种框架识别。

**Q: 遥测数据会上传吗？**

A: 不会。所有遥测数据仅存储在本地 SQLite 数据库中，不会上传到任何服务器。

## Contributing

1. Fork 本仓库
2. 创建功能分支：`git checkout -b agent/PROJ-xxx-description`
3. 提交变更：遵循 Conventional Commits 规范
4. 推送分支：`git push origin agent/PROJ-xxx-description`
5. 创建 Pull Request

请确保所有测试通过：`pytest tests/ -v`

## License

[MIT](https://github.com/weksbwrx62862/dev-lifecycle/blob/main/LICENSE)

## Security

如发现安全漏洞，请通过 GitHub Issues 私密报告，不要在公开 Issue 中暴露敏感信息。本插件不收集、不上传任何用户数据。

## 致谢

- [Hermes Agent](https://github.com/weksbwrx62862/hermes) — 插件运行框架
- 所有贡献者和技能作者

<p align="center"><strong>Dev Lifecycle — 让 AI Agent 按规范走完每一步</strong></p>
