# SmartDorm 数据库设计

## 设计原则

- 数据库使用 PostgreSQL。
- 主键建议使用 UUID，时间字段统一包含 `created_at`、`updated_at`。
- 重要业务表保留状态字段，并通过审计日志记录关键变更。
- 枚举值先在文档中约定，后续可用数据库约束或应用层枚举实现。
- 所有外键命名保持清晰，便于 SQLAlchemy 模型映射。

## 核心枚举

- 用户状态：`active`、`disabled`、`locked`
- 床位状态：`available`、`occupied`、`maintenance`
- 报修状态：`submitted`、`approved`、`rejected`、`assigned`、`in_progress`、`completed`、`closed`
- 工单状态：`pending_assign`、`assigned`、`accepted`、`in_progress`、`completed`、`closed`、`cancelled`
- 紧急程度：`low`、`normal`、`high`、`urgent`
- 告警状态：`open`、`processing`、`resolved`、`ignored`
- 巡检状态：`draft`、`in_progress`、`completed`
- 通知状态：`draft`、`published`、`revoked`

## 表结构

### users 用户

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 用户 ID |
| username | varchar unique | 登录名 |
| password_hash | varchar | 密码哈希 |
| display_name | varchar | 显示名称 |
| phone | varchar | 手机号 |
| email | varchar | 邮箱 |
| status | varchar | 用户状态 |
| last_login_at | timestamptz | 最近登录时间 |
| created_at / updated_at | timestamptz | 创建和更新时间 |

### roles 角色

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 角色 ID |
| code | varchar unique | 角色标识 |
| name | varchar | 角色名称 |
| description | text | 描述 |
| created_at / updated_at | timestamptz | 创建和更新时间 |

### permissions 权限

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 权限 ID |
| code | varchar unique | 权限标识 |
| name | varchar | 权限名称 |
| module | varchar | 所属模块 |
| description | text | 描述 |

### user_roles 用户角色

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| user_id | uuid fk users.id | 用户 |
| role_id | uuid fk roles.id | 角色 |
| created_at | timestamptz | 创建时间 |

联合主键：`user_id`、`role_id`。

### role_permissions 角色权限

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| role_id | uuid fk roles.id | 角色 |
| permission_id | uuid fk permissions.id | 权限 |
| created_at | timestamptz | 创建时间 |

联合主键：`role_id`、`permission_id`。

### campuses 校区

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 校区 ID |
| name | varchar | 校区名称 |
| address | varchar | 地址 |
| status | varchar | 状态 |

### dorm_buildings 宿舍楼

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 楼栋 ID |
| campus_id | uuid fk campuses.id | 所属校区 |
| name | varchar | 楼栋名称 |
| code | varchar unique | 楼栋编码 |
| gender_type | varchar | 男寝、女寝或混合 |
| manager_user_id | uuid fk users.id | 负责宿管 |
| status | varchar | 状态 |

### dorm_floors 楼层

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 楼层 ID |
| building_id | uuid fk dorm_buildings.id | 所属楼栋 |
| floor_no | integer | 楼层号 |
| name | varchar | 楼层名称 |

### dorm_rooms 房间

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 房间 ID |
| building_id | uuid fk dorm_buildings.id | 所属楼栋 |
| floor_id | uuid fk dorm_floors.id | 所属楼层 |
| room_no | varchar | 房间号 |
| capacity | integer | 容量 |
| status | varchar | 状态 |

### beds 床位

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 床位 ID |
| room_id | uuid fk dorm_rooms.id | 所属房间 |
| bed_no | varchar | 床位号 |
| status | varchar | 床位状态 |

### student_profiles 学生档案

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 学生档案 ID |
| user_id | uuid fk users.id | 关联用户 |
| student_no | varchar unique | 学号 |
| college | varchar | 学院 |
| major | varchar | 专业 |
| class_name | varchar | 班级 |
| enrollment_year | integer | 入学年份 |

### check_ins 入住记录

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 入住 ID |
| student_id | uuid fk student_profiles.id | 学生 |
| room_id | uuid fk dorm_rooms.id | 房间 |
| bed_id | uuid fk beds.id | 床位 |
| check_in_date | date | 入住日期 |
| check_out_date | date nullable | 退宿日期 |
| status | varchar | 状态 |

### repair_reports 报修单

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 报修 ID |
| reporter_user_id | uuid fk users.id | 报修人 |
| room_id | uuid fk dorm_rooms.id | 房间 |
| category | varchar | 报修类型 |
| urgency | varchar | 紧急程度 |
| description | text | 问题描述 |
| status | varchar | 报修状态 |
| reject_reason | text nullable | 驳回原因 |
| rating | integer nullable | 评价分 |
| feedback | text nullable | 评价内容 |

### repair_work_orders 维修工单

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 工单 ID |
| report_id | uuid fk repair_reports.id | 来源报修 |
| assignee_user_id | uuid fk users.id | 维修人员 |
| assigned_by_user_id | uuid fk users.id | 派单人 |
| priority | varchar | 优先级 |
| status | varchar | 工单状态 |
| scheduled_at | timestamptz nullable | 计划时间 |
| accepted_at | timestamptz nullable | 接单时间 |
| completed_at | timestamptz nullable | 完成时间 |
| result | text nullable | 处理结果 |

### energy_meters 能耗表计

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 表计 ID |
| meter_no | varchar unique | 表计编号 |
| meter_type | varchar | 水、电等类型 |
| building_id | uuid fk dorm_buildings.id | 所属楼栋 |
| room_id | uuid fk dorm_rooms.id nullable | 所属房间 |
| unit | varchar | 单位 |
| status | varchar | 状态 |

### energy_readings 能耗读数

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 读数 ID |
| meter_id | uuid fk energy_meters.id | 表计 |
| reading_value | numeric | 当前读数 |
| consumption | numeric | 区间消耗 |
| read_at | timestamptz | 读数时间 |
| source | varchar | 来源 |

### energy_alerts 能耗异常告警

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 告警 ID |
| meter_id | uuid fk energy_meters.id | 表计 |
| building_id | uuid fk dorm_buildings.id | 楼栋 |
| room_id | uuid fk dorm_rooms.id nullable | 房间 |
| alert_type | varchar | 告警类型 |
| level | varchar | 告警等级 |
| message | text | 告警内容 |
| status | varchar | 告警状态 |
| handled_by_user_id | uuid fk users.id nullable | 处理人 |
| handled_at | timestamptz nullable | 处理时间 |

### inspections 安全巡检

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 巡检 ID |
| building_id | uuid fk dorm_buildings.id | 楼栋 |
| inspector_user_id | uuid fk users.id | 巡检人 |
| inspected_at | timestamptz | 巡检时间 |
| status | varchar | 巡检状态 |
| summary | text | 总结 |

### inspection_items 巡检项

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 巡检项 ID |
| inspection_id | uuid fk inspections.id | 巡检记录 |
| item_name | varchar | 项目名称 |
| result | varchar | 结果 |
| issue_description | text nullable | 异常描述 |
| rectification_status | varchar | 整改状态 |

### notices 通知公告

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 公告 ID |
| title | varchar | 标题 |
| content | text | 内容 |
| target_type | varchar | 目标范围 |
| status | varchar | 状态 |
| published_by_user_id | uuid fk users.id | 发布人 |
| published_at | timestamptz nullable | 发布时间 |

### notifications 站内通知

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 通知 ID |
| user_id | uuid fk users.id | 接收人 |
| title | varchar | 标题 |
| content | text | 内容 |
| biz_type | varchar | 业务类型 |
| biz_id | uuid nullable | 业务 ID |
| read_at | timestamptz nullable | 已读时间 |

### audit_logs 审计日志

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 日志 ID |
| actor_user_id | uuid fk users.id nullable | 操作人 |
| action | varchar | 操作 |
| resource_type | varchar | 资源类型 |
| resource_id | uuid nullable | 资源 ID |
| ip_address | varchar | IP |
| user_agent | varchar | UA |
| detail | jsonb | 详情 |
| created_at | timestamptz | 创建时间 |

### operation_metrics 运营指标

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid pk | 指标 ID |
| metric_date | date | 指标日期 |
| scope_type | varchar | 范围类型 |
| scope_id | uuid nullable | 范围 ID |
| metric_key | varchar | 指标键 |
| metric_value | numeric | 指标值 |
| extra | jsonb | 扩展数据 |

## 关系概览

- 一个校区包含多个宿舍楼。
- 一个宿舍楼包含多个楼层、房间和能耗表计。
- 一个房间包含多个床位，可以绑定能耗表计。
- 一个学生档案关联一个用户，入住记录关联房间和床位。
- 一个报修单可以生成一个维修工单。
- 一个工单分配给一个维修人员。
- 一个表计产生多条读数，并可能产生多条告警。
- 一次巡检包含多个巡检项。
- 角色和权限通过多对多关系控制访问。
