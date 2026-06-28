# SmartDorm 答辩讲解材料

## 30 秒项目介绍

SmartDorm 是一个智慧宿舍报修与能耗运维管理平台，面向学生、宿管、维修人员、后勤管理员和系统管理员。系统把宿舍资产、入住、报修工单、能耗监测、安全巡检和通知公告整合在一个中后台平台中，通过数据看板展示运营状态，并通过 RBAC 权限模型保证不同角色只访问自己的业务范围。

## 演示主线

1. 使用学生账号登录，进入 `/student`。
2. 学生提交一条宿舍报修，查看状态为已提交。
3. 切换宿管员账号，进入 `/manager`，审核该报修。
4. 后勤管理员或宿管员派单给维修人员。
5. 维修人员进入 `/worker`，接单并完成工单。
6. 学生确认并评价，报修闭环完成。
7. 后勤管理员进入 `/dashboard` 查看报修趋势、工单效率和能耗告警。

## 架构讲解

系统采用前后端分离架构：

- 前端使用 Vue3、Vite、TypeScript、Pinia 和 ECharts。
- 后端使用 FastAPI、SQLAlchemy、Pydantic 和 Alembic。
- 数据库使用 PostgreSQL。
- 运行环境通过 Docker Compose 编排，保证演示环境可复现。

## 数据库讲解

数据库分为五类：

- 身份权限：`users`、`roles`、`permissions`、`user_roles`、`role_permissions`。
- 宿舍资产：`campuses`、`dorm_buildings`、`dorm_floors`、`dorm_rooms`、`beds`。
- 入住报修：`student_profiles`、`check_ins`、`repair_reports`、`repair_work_orders`。
- 能耗巡检：`energy_meters`、`energy_readings`、`energy_alerts`、`inspections`、`inspection_items`。
- 通知审计：`notices`、`notifications`、`audit_logs`、`operation_metrics`。

## 权限亮点

系统使用 RBAC 权限控制。学生只能查看自己的报修和通知；宿管员只能管理负责楼栋；维修人员只能处理分配给自己的工单；后勤管理员管理全局业务数据；系统管理员负责用户、角色、权限和审计。

## 工程亮点

- 使用 Docker-only 运行约束，避免环境不一致。
- 前端正式模式关闭 mock fallback，接口失败展示真实错误。
- 401 自动清理 token 并跳转登录页。
- 写操作失败不会更新本地假状态。
- 关键业务操作写入审计日志，便于追踪责任。

## 可能问答

### 为什么选择 FastAPI？

FastAPI 类型提示友好，自动生成 OpenAPI 文档，适合快速实现结构清晰的 REST API，也便于和 Pydantic schema 配合。

### 为什么需要 RBAC？

宿舍管理系统涉及学生隐私、维修任务和系统配置。RBAC 可以把功能权限和数据范围分离，避免学生看到他人数据，也避免维修人员访问系统管理功能。

### 能耗异常如何判断？

第一阶段可以通过阈值和环比突增判断，例如单日用电超过阈值或较历史均值增长超过一定比例。后续可以扩展为时序模型或 IoT 设备实时接入。

### 系统如何保证演示稳定？

使用 Docker Compose 固化运行环境，使用种子数据准备演示账号和业务数据。正式模式默认不启用 mock fallback，只有明确设置演示开关时才使用兜底数据。
