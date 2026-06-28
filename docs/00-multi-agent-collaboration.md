# SmartDorm 多 Agent 协作说明

## 项目边界

SmartDorm 是独立项目，不能混用 LabOps 的文件、提交、分支、提示词和业务上下文。所有产物必须位于 `D:\Codex\SmartDorm` 工作区内。

## 主线程职责

主线程负责统一集成、验收、提交和推送。任何子 Agent 产出的内容都需要由主线程合并检查，重点关注：

- 是否符合 SmartDorm 项目定位。
- 是否遵守 Docker-only 运行约束。
- 是否和既有文档、数据模型、API 命名一致。
- 是否引入了 mock 成功、静默吞错或错误业务状态。
- 是否误改了非本任务范围的文件。

## Agent 分工

| Agent | 主要职责 | 负责范围 |
| --- | --- | --- |
| A1 产品文档 Agent | 需求、模块、角色权限、流程、答辩材料 | `docs/01`、`docs/02`、`docs/03`、`docs/04`、`docs/07` |
| A2 数据库架构 Agent | PostgreSQL 表结构、ER 关系、迁移、种子数据 | `docs/05`、`backend/app/models/`、`backend/app/db/`、`backend/alembic/` |
| A3 后端 API Agent | FastAPI 接口、RBAC、业务 service、测试 | `backend/app/api/`、`backend/app/schemas/`、`backend/app/services/`、`backend/tests/` |
| A4 前端 UI Agent | Vue3 页面、路由、Pinia、API 封装、ECharts | `frontend/src/` |
| A5 Docker 验证 Agent | 容器化配置、环境变量、部署验证 | `docker-compose.yml`、`.env.example`、Dockerfile、部署说明 |

## 多聊天窗口协作规则

后续多 Agent 协作采用“一个职责 Agent 一个新聊天窗口”的方式推进。主线程不在同一个窗口里同时扮演多个责任 Agent，而是负责拆分任务、提供上下文、验收结果和统一集成。

- A1 到 A5 每个 Agent 都应在独立聊天窗口中启动。
- 新窗口启动时必须明确说明当前负责范围、可修改文件、禁止修改文件和 Docker-only 约束。
- 子 Agent 只处理自己的职责边界，不主动扩展到其他 Agent 的目录。
- 子 Agent 完成后返回修改清单、验证方式、风险点和需要主线程继续处理的问题。
- 主线程收到子 Agent 结果后，先检查差异，再决定是否合并、修正或要求该窗口继续迭代。
- 需要并行推进时，可以同时开启多个聊天窗口，但同一文件原则上只分配给一个 Agent。

推荐窗口命名：

- `SmartDorm-A1-产品文档`
- `SmartDorm-A2-数据库架构`
- `SmartDorm-A3-后端API`
- `SmartDorm-A4-前端UI`
- `SmartDorm-A5-Docker验证`

## 协作流程

1. 主线程先建立需求、数据库和 API 基线。
2. 主线程按职责创建或安排独立聊天窗口，并交付对应提示词。
3. 子 Agent 按任务边界修改文件，避免跨域重构。
4. 子 Agent 完成后给出修改清单、验证命令和风险点。
5. 主线程统一运行容器内验证并整合冲突。
6. 主线程确认满足答辩展示目标后再提交。

## 运行与验证规则

- 所有安装、测试、构建、服务启动都必须在 Docker 容器内完成。
- 宿主机只允许执行 Git、Docker、Docker Compose 以及只读检查命令。
- 不允许在宿主机执行 `npm install`、`pip install`、`pytest`、`npm run build` 等项目命令。
- 默认 `VITE_ENABLE_MOCK_FALLBACK=false`。
- 只有答辩演示显式开启 `VITE_ENABLE_MOCK_FALLBACK=true` 时，前端才允许使用本地兜底数据。

## 交付质量标准

- 文档、数据库、API、前端页面命名保持统一。
- 写操作失败必须显示真实错误，不能更新本地假状态。
- 认证过期或 401 必须清除 token 并跳转登录页。
- 关键业务状态应有审计日志。
- 看板数据应来自后端聚合接口，前端只负责展示和轻量格式化。
