# SmartDorm Agent 任务看板

## 当前阶段

第一阶段：文档、目录骨架和开发路线。

## 总控主线程

| 任务 | 状态 | 备注 |
| --- | --- | --- |
| 接管 SmartDorm 工作区 | 完成 | 当前可见工作区为空，已建立基线 |
| 补齐 00-09 文档 | 完成 | 第一阶段交付 |
| 检查 README 和 `.env.example` | 完成 | 强调 Docker-only 与 mock fallback |
| 明确多窗口 Agent 协作方式 | 完成 | 后续不同职责 Agent 使用独立聊天窗口 |
| 规划下一阶段 | 待办 | 数据库与 API 实现 |

## 多聊天窗口安排

| 窗口 | Agent | 启动时机 | 状态 |
| --- | --- | --- | --- |
| `SmartDorm-A1-产品文档` | A1 产品文档 Agent | 需求或答辩材料需要继续细化时 | 待创建 |
| `SmartDorm-A2-数据库架构` | A2 数据库架构 Agent | 开始 SQLAlchemy、Alembic、种子数据时 | 已创建：`019f12ad-8de5-72f1-a518-9c5237b05b11` |
| `SmartDorm-A3-后端API` | A3 后端 API Agent | 开始 FastAPI 接口、RBAC、测试时 | 待创建 |
| `SmartDorm-A4-前端UI` | A4 前端 UI Agent | 开始 Vue3 页面、路由、看板和报修流程时 | 待创建 |
| `SmartDorm-A5-Docker验证` | A5 Docker 验证 Agent | 开始 Docker Compose、Dockerfile、容器验证时 | 已创建：`019f12ad-730c-7781-bca8-9c6c0198c411` |

主线程负责创建或安排这些窗口，并在每个窗口交付 `docs/09-codex-prompts.md` 中对应提示词。子 Agent 不负责最终提交，最终集成、验收和提交由主线程完成。

## A1 产品文档 Agent

| 任务 | 状态 | 产物 |
| --- | --- | --- |
| 完善需求说明 | 完成 | `docs/01-requirements.md` |
| 完善模块说明 | 完成 | `docs/02-modules.md` |
| 完善角色权限 | 完成 | `docs/03-roles-permissions.md` |
| 完善业务流程 | 完成 | `docs/04-business-flow.md` |
| 编写答辩材料 | 完成 | `docs/07-defense-script.md` |

## A2 数据库架构 Agent

| 任务 | 状态 | 产物 |
| --- | --- | --- |
| 数据库表结构设计 | 完成 | `docs/05-database-design.md` |
| SQLAlchemy 模型 | 待办 | `backend/app/models/` |
| Alembic 迁移 | 待办 | `backend/alembic/` |
| 种子数据 | 待办 | `backend/app/db/` |

## A3 后端 API Agent

| 任务 | 状态 | 产物 |
| --- | --- | --- |
| REST API 规划 | 完成 | `docs/06-api-spec.md` |
| FastAPI 项目骨架 | 待办 | `backend/app/` |
| 认证和 RBAC | 待办 | `backend/app/core/` |
| 业务 service 和测试 | 待办 | `backend/app/services/`、`backend/tests/` |

## A4 前端 UI Agent

| 任务 | 状态 | 产物 |
| --- | --- | --- |
| Vue3 项目骨架 | 待办 | `frontend/` |
| 路由和 Pinia | 待办 | `frontend/src/` |
| 看板和图表 | 待办 | `frontend/src/views/` |
| 错误处理和 mock fallback 开关 | 待办 | `frontend/src/api/` |

## A5 Docker 验证 Agent

| 任务 | 状态 | 产物 |
| --- | --- | --- |
| Docker Compose 规划 | 待办 | `docker-compose.yml` |
| 后端 Dockerfile | 待办 | `backend/Dockerfile` |
| 前端 Dockerfile | 待办 | `frontend/Dockerfile` |
| 容器内验证命令 | 待办 | 部署说明 |

## 下一阶段建议

1. 先完成 `docker-compose.yml`、后端 Dockerfile、前端 Dockerfile 和数据库服务。
2. 搭建 FastAPI 最小可运行骨架，并在容器内启动。
3. 建立 SQLAlchemy 模型和 Alembic 首版迁移。
4. 准备演示账号和种子数据。
5. 再实现前端登录、布局、看板和核心报修闭环。
