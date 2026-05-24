# Dev Lifecycle

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-blue.svg" alt="Python">
  <img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License">
  <img src="https://img.shields.io/badge/Hermes-%3E%3D2.0.0-orange.svg" alt="Hermes">
</p>

软件开发生命周期技能包 v2 — 覆盖从需求到交付的全链路工作流。

## 工作流

```
grill → PRD → plan → prototype → TDD → debug → review → triage → handoff
```

## v2 新特性

- 工作流状态机：`start` / `advance` / `rollback` / `resume` / `report`
- 质量门禁（Quality Gates）
- 项目上下文感知
- 遥测统计

## 快速开始

```yaml
# hermes_config.yaml
plugins:
  - name: dev-lifecycle
    path: ./dev-lifecycle
```

## License

MIT