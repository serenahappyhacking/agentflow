# AgentFlow — AI-Native Enterprise Work Platform

> 将模型能力深度融入企业工作流，用 AI Agent 替代运维工程师和外包研发

## 项目定位

B端 AI 原生工作平台，通过飞书审批/对话驱动，自动化完成云资源管理、CI/CD 流水线创建、域名管理等运维操作，并通过 LLM Coding Agent 实现需求到上线的全自动化。

**核心目标**: 用 AI 替代运维团队（2人）+ 外包研发团队，实现零人工介入的企业 IT 运营。

## 两个 Phase

### Phase 1: 运维自动化引擎（已完成）

飞书审批驱动的多云资源自动开通，替代运维工程师的手动操作。

- **云资源开通**: 业务负责人飞书提交审批 → 主管审批 → 自动调云 API 创建资源 → 飞书通知连接信息
- **CI/CD 流水线创建**: 研发提交部署申请 → 自动在云效创建流水线 + 分配域名
- **域名替换**: 自动分配域名 → 等保备案后自动更新 DNS + SSL
- **定时任务**: 每日资源到期预警 + 每月成本报表

支持多云: 阿里云（RDS / Redis / ECS / OSS / SLB / SAE）、华为云（昇腾 GPU）、腾讯云（CVM）

### Phase 2: AI Coding Agent + 对话式云操作（规划中）

- **AI 对话式云操作**: 飞书群 `@AgentFlow 帮我开一个4G Redis` 一句话替代填审批表单
- **LLM Vibe Coding**: 业务描述需求 → Coding Agent 写码 → Review Agent 审查 → 自动部署上线
- **双模型交叉审查**: Claude 做代码生成，DeepSeek 做代码审查，不同模型交叉验证

## 技术架构

```
飞书审批/对话 → POST /api/lark/events → 工作流路由 → 云 API 调用 → 飞书通知
                                                       ↓
                                                 SQLite 资源台账
```

| 组件 | 技术 |
|------|------|
| 后端 | Python 3.12 + FastAPI |
| 数据库 | SQLite（可切 PostgreSQL） |
| 飞书 SDK | lark-oapi |
| 阿里云 | alibabacloud-devops / alidns / rds / ecs 等 |
| 华为云 | huaweicloudsdkecs |
| 腾讯云 | tencentcloud-sdk-python |
| 定时任务 | APScheduler |

## 项目结构

```
agentflow/
├── main.py                     # FastAPI 入口
├── config.py                   # 配置管理
├── scheduler.py                # 定时任务（到期提醒 + 成本报表）
├── api/
│   └── lark_events.py          # 飞书事件回调（系统入口）
├── workflows/
│   ├── pipeline_setup.py       # 流程1: 创建云效流水线
│   ├── resource_provision.py   # 流程2: 多云资源开通
│   └── domain_change.py        # 流程3: 域名替换
├── cloud/
│   ├── alibaba/                # 阿里云 API（10个模块）
│   ├── huawei/                 # 华为云 API（GPU）
│   └── tencent/                # 腾讯云 API
├── lark/
│   ├── client.py               # 飞书 API 客户端
│   ├── approval_templates.py   # 审批表单字段定义
│   └── notifier.py             # 飞书通知
├── models/
│   ├── pipeline_record.py      # 流水线记录
│   ├── resource_record.py      # 资源台账
│   └── operation_log.py        # 操作审计日志
└── docs/
    ├── phase2-plan.md          # Phase 2 规划
    └── workflow-diagrams.md    # Mermaid 流程图（10张）
```

## 快速开始

### 1. 安装

```bash
cd agentflow
pip install -e .
```

### 2. 配置

```bash
cp .env.example .env
```

编辑 `.env`，填入飞书应用凭证和云平台 AccessKey。完整配置项见 [.env.example](.env.example)。

### 3. 启动

```bash
# 开发模式
python main.py

# 生产模式
uvicorn main:app --host 0.0.0.0 --port 8000

# Docker
docker build -t agentflow .
docker run -p 8000:8000 --env-file .env agentflow
```

### 4. 验证

```bash
curl http://localhost:8000/health
# {"status":"ok"}
```

## 流程图

详见 [docs/workflow-diagrams.md](docs/workflow-diagrams.md)，包含 10 张 Mermaid 流程图：

1. 现状 vs 自动化对比
2. 项目全生命周期
3. 云资源开通流程
4. 域名替换流程
5. 定时任务流程
6. 系统整体架构
7. AI 对话式云操作（Phase 2）
8. LLM Vibe Coding（Phase 2）
9. Phase 2 完整架构
10. 里程碑总览

## 价值

| 维度 | 人工 | Phase 1 自动化 | Phase 2 AI 驱动 |
|------|------|---------------|----------------|
| **运维人力** | 2 人 | 0 人 | 0 人 |
| **研发人力** | 外包 RD 团队 | 外包 RD 团队 | LLM 替代 |
| **操作方式** | 口头找运维 | 飞书审批表单 | 飞书对话一句话 |
| **响应速度** | 几小时到几天 | 分钟级 | 秒级 |
