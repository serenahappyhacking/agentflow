# AgentFlow — AI-Native Enterprise Work Platform

> Deeply integrate LLM capabilities into enterprise workflows. Replace ops engineers and outsourced developers with AI Agents.

## What is AgentFlow

An AI-native B2B work platform that automates cloud resource management, CI/CD pipeline creation, and domain management through IM approval/chat-driven workflows, while leveraging LLM Coding Agents to achieve fully automated requirement-to-deployment pipelines.

**Core Goal**: Replace the ops team (2 engineers) + outsourced dev team with AI, achieving zero-human-intervention enterprise IT operations.

## Two Phases

### Phase 1: DevOps Automation Engine (Completed)

IM-approval-driven multi-cloud resource provisioning, replacing manual ops work.

- **Cloud Resource Provisioning**: Business owner submits approval → Manager approves → Auto-call cloud APIs to create resources → IM notification with connection info
- **CI/CD Pipeline Creation**: Developer submits deployment request → Auto-create Yunxiao pipeline + assign domain
- **Domain Replacement**: Temp domain → After security compliance filing, auto-update DNS + SSL
- **Scheduled Tasks**: Daily resource expiry alerts + Monthly cost reports

Multi-cloud support: Alibaba Cloud (RDS / Redis / ECS / OSS / SLB / SAE), Huawei Cloud (Ascend GPU, 910B 910C), Tencent Cloud (CVM)

### Phase 2: AI Coding Agent + Conversational Cloud Ops (Planned)

- **Conversational Cloud Ops**: `@AgentFlow provision a 4G Redis` — one message replaces a 7-field approval form
- **LLM Vibe Coding**: Business describes requirement → Coding Agent writes code → Review Agent reviews → Auto-deploy to production
- **Dual-Model Cross Review**: Claude for code generation, DeepSeek for code review — different models for cross-validation

## Architecture

```
IM Approval/Chat → POST /api/lark/events → Workflow Router → Cloud API Calls → IM Notification
                                                                  ↓
                                                           SQLite Inventory
```

| Component | Technology |
|-----------|-----------|
| Backend | Python 3.12 + FastAPI |
| Database | SQLite (PostgreSQL ready) |
| IM SDK | lark-oapi |
| Alibaba Cloud | alibabacloud-devops / alidns / rds / ecs etc. |
| Huawei Cloud | huaweicloudsdkecs |
| Tencent Cloud | tencentcloud-sdk-python |
| Scheduler | APScheduler |

## Project Structure

```
agentflow/
├── main.py                     # FastAPI entrypoint
├── config.py                   # Configuration management
├── scheduler.py                # Scheduled tasks (expiry alerts + cost reports)
├── api/
│   └── lark_events.py          # IM event callback (system entry point)
├── workflows/
│   ├── pipeline_setup.py       # Workflow 1: Create CI/CD pipeline
│   ├── resource_provision.py   # Workflow 2: Multi-cloud resource provisioning
│   └── domain_change.py        # Workflow 3: Domain replacement
├── cloud/
│   ├── alibaba/                # Alibaba Cloud API (10 modules)
│   ├── huawei/                 # Huawei Cloud API (GPU)
│   └── tencent/                # Tencent Cloud API
├── lark/
│   ├── client.py               # IM API client
│   ├── approval_templates.py   # Approval form field definitions
│   └── notifier.py             # IM notifications
├── models/
│   ├── pipeline_record.py      # Pipeline records
│   ├── resource_record.py      # Resource inventory
│   └── operation_log.py        # Audit log
└── docs/
    ├── phase2-plan.md          # Phase 2 roadmap
    └── workflow-diagrams.md    # Mermaid diagrams (10 charts)
```

## Getting Started

### 1. Install

```bash
cd agentflow
pip install -e .
```

### 2. Configure

```bash
cp .env.example .env
```

Edit `.env` with your IM app credentials and cloud platform AccessKeys. See [.env.example](.env.example) for all config options.

### 3. Run

```bash
# Development
python main.py

# Production
uvicorn main:app --host 0.0.0.0 --port 8000

# Docker
docker build -t agentflow .
docker run -p 8000:8000 --env-file .env agentflow
```

### 4. Verify

```bash
curl http://localhost:8000/health
# {"status":"ok"}
```

## Workflow Diagrams

See [docs/workflow-diagrams.md](docs/workflow-diagrams.md) for 10 Mermaid diagrams:

1. Current State vs Automated Comparison
2. Full Project Lifecycle
3. Cloud Resource Provisioning Flow
4. Domain Replacement Flow
5. Scheduled Tasks Flow
6. System Architecture Overview
7. AI Conversational Cloud Ops (Phase 2)
8. LLM Vibe Coding (Phase 2)
9. Phase 2 Full Architecture
10. Milestone Overview

## Impact

| Dimension | Manual | Phase 1 Automated | Phase 2 AI-Driven |
|-----------|--------|-------------------|-------------------|
| **Ops Headcount** | 2 engineers | 0 | 0 |
| **Dev Headcount** | Outsourced team | Outsourced team | LLM replaces |
| **Interface** | Verbal requests to ops | IM approval form | One-line chat message |
| **Response Time** | Hours to days | Minutes | Seconds |
