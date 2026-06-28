# SmartDorm

SmartDorm 是一个面向考研复试展示的智慧宿舍报修与能耗运维管理平台。系统定位为数据看板型中后台，覆盖宿舍楼宇、学生入住、报修、维修工单、能耗监测、安全巡检、通知公告、数据分析和系统管理等场景。

## 技术栈

- 后端：FastAPI、SQLAlchemy、Pydantic、Alembic
- 前端：Vue 3、Vite、TypeScript、Pinia、ECharts
- 数据库：PostgreSQL
- 部署：Docker、Docker Compose

## 运行约束

所有运行、测试、构建都必须在 Docker 容器中完成。宿主机只需要安装 Git、Docker 和 Docker Compose。

禁止在宿主机直接执行项目依赖、测试或构建命令，例如：

- `npm install`
- `pip install`
- `pytest`
- `npm run build`

正式运行默认关闭 mock fallback：

```env
VITE_ENABLE_MOCK_FALLBACK=false
```

正式模式下，接口失败必须显示真实错误；`401 Unauthorized` 时前端必须清除 token 并跳转登录页；业务写操作失败时不能修改本地 mock 状态，也不能提示成功。只有显式设置 `VITE_ENABLE_MOCK_FALLBACK=true` 时，才允许启用答辩演示数据兜底。

## 角色入口

- 学生：`/student`
- 宿管员：`/manager`
- 维修人员：`/worker`
- 后勤管理员：`/dashboard`
- 系统管理员：`/dashboard`

## 文档导航

- [多 Agent 协作](docs/00-multi-agent-collaboration.md)
- [需求说明](docs/01-requirements.md)
- [功能模块](docs/02-modules.md)
- [角色权限](docs/03-roles-permissions.md)
- [业务流程](docs/04-business-flow.md)
- [数据库设计](docs/05-database-design.md)
- [API 规划](docs/06-api-spec.md)
- [答辩讲解](docs/07-defense-script.md)
- [任务看板](docs/08-agent-task-board.md)
- [Codex 提示词](docs/09-codex-prompts.md)

## 阶段路线

1. 完成需求、模块、权限、流程、数据库和 API 文档。
2. 建立 PostgreSQL 表结构、SQLAlchemy 模型、Alembic 迁移和种子数据。
3. 实现 FastAPI 认证、RBAC、业务接口和测试。
4. 实现 Vue 3 中后台界面、路由、状态管理、图表和错误处理。
5. 使用 Docker Compose 联调、构建和验收。
