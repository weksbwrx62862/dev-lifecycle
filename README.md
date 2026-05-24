# Dev Lifecycle

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-blue.svg" alt="Python">
  <img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License">
  <img src="https://img.shields.io/badge/Hermes-%3E%3D2.0.0-orange.svg" alt="Hermes">
  <img src="https://img.shields.io/badge/version-2.0.0-blue.svg" alt="Version">
</p>

软件开发生命周期技能包 v2 — 覆盖从需求到交付的全链路工作流，支持工作流状态机、质量门禁、项目上下文感知和遥测统计。

## 工作流

```
grill → PRD → plan → prototype → TDD → debug → review → triage → handoff
```

每个阶段都有独立的质量门禁（Quality Gates），自动检查准入条件，确保流程严谨可控。

## v2 新特性

- **工作流状态机**：`start` / `advance` / `rollback` / `resume` / `report` 完整生命周期
- **质量门禁**：每个阶段自动检查准入条件，不满足则阻断前进
- **项目上下文感知**：自动理解项目结构和约定，减少手动配置
- **遥测统计**：追踪工作流耗时、吞吐量、阻塞点

## 安装

### 前置条件

- Python 3.10+
- [Hermes Agent](https://github.com/weksbwrx62862/hermes) >= 2.0.0

### 从源码安装

```bash
git clone https://github.com/weksbwrx62862/dev-lifecycle.git
cd dev-lifecycle
pip install -e .
```

### 安装依赖

```bash
pip install pyyaml
```

## 使用

### Hermes 插件模式

在 Hermes 配置中启用：

```yaml
# hermes_config.yaml
plugins:
  - name: dev-lifecycle
    path: ./dev-lifecycle
```

### 工作流操作

```yaml
# 启动一个新的开发流程
dev_workflow start --name my-project

# 推进到下一阶段
dev_workflow advance

# 回滚到前一阶段
dev_workflow rollback

# 查看当前状态
dev_workflow report
```

## 提供的工具

| 工具 | 功能 |
|------|------|
| `dev_workflow` | 工作流主入口：start/advance/rollback/resume/report |

## 提供的钩子

| 钩子 | 说明 |
|------|------|
| `on_session_start` | 会话启动时恢复工作流状态 |

## 项目结构

```
dev-lifecycle/
├── plugin.yaml          # 插件声明
├── config.py            # 配置管理
├── constants.py         # 工作流常量
├── context.py           # 项目上下文感知
├── gates.py             # 质量门禁
├── handlers.py          # 工具处理器
├── schemas.py           # 数据模式
├── state.py             # 工作流状态机
├── telemetry.py         # 遥测统计
└── tests/               # 测试套件
    └── test_workflow.py
```

## 开发

```bash
git clone https://github.com/weksbwrx62862/dev-lifecycle.git
cd dev-lifecycle
pip install -e ".[dev]"
pytest tests/ -v
```

## License

MIT