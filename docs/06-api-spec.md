# SmartDorm API 规划

## 通用约定

- API 前缀：`/api/v1`
- 认证方式：`Authorization: Bearer <token>`
- 响应格式：业务接口返回 JSON。
- 分页参数：`page`、`page_size`。
- 排序参数：`sort`，例如 `created_at:desc`。
- 错误响应应包含 `code`、`message`、`detail`。

## 1. 认证接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| POST | `/auth/login` | 登录，返回 token 和用户信息 |
| POST | `/auth/logout` | 登出 |
| POST | `/auth/refresh` | 刷新 token |

## 2. 当前用户接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/me` | 当前用户、角色、权限、菜单 |
| PATCH | `/me/profile` | 更新个人资料 |
| PATCH | `/me/password` | 修改密码 |

## 3. 首页看板接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/dashboard/summary` | 指标卡汇总 |
| GET | `/dashboard/repair-trend` | 报修趋势 |
| GET | `/dashboard/energy-trend` | 能耗趋势 |
| GET | `/dashboard/todos` | 当前角色待办 |

## 4. 楼宇/房间/床位接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/campuses` | 校区列表、创建 |
| GET/PATCH/DELETE | `/campuses/{id}` | 校区详情、更新、删除 |
| GET/POST | `/buildings` | 楼栋列表、创建 |
| GET/PATCH/DELETE | `/buildings/{id}` | 楼栋详情、更新、删除 |
| GET/POST | `/floors` | 楼层列表、创建 |
| GET/POST | `/rooms` | 房间列表、创建 |
| GET/PATCH/DELETE | `/rooms/{id}` | 房间详情、更新、删除 |
| GET/POST | `/beds` | 床位列表、创建 |
| PATCH | `/beds/{id}/status` | 更新床位状态 |

## 5. 学生入住接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/students` | 学生档案列表、创建 |
| GET/PATCH/DELETE | `/students/{id}` | 学生详情、更新、删除 |
| GET/POST | `/check-ins` | 入住记录列表、办理入住 |
| POST | `/check-ins/{id}/checkout` | 办理退宿 |

## 6. 报修接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/repair-reports` | 报修列表、提交报修 |
| GET | `/repair-reports/{id}` | 报修详情 |
| POST | `/repair-reports/{id}/approve` | 审核通过 |
| POST | `/repair-reports/{id}/reject` | 驳回报修 |
| POST | `/repair-reports/{id}/rate` | 学生评价 |

## 7. 工单接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/work-orders` | 工单列表、创建工单 |
| GET | `/work-orders/{id}` | 工单详情 |
| POST | `/work-orders/{id}/assign` | 派单 |
| POST | `/work-orders/{id}/accept` | 接单 |
| POST | `/work-orders/{id}/start` | 开始处理 |
| POST | `/work-orders/{id}/complete` | 完成工单 |
| POST | `/work-orders/{id}/close` | 关闭工单 |

## 8. 能耗监测接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/energy/meters` | 表计列表、创建 |
| GET/PATCH | `/energy/meters/{id}` | 表计详情、更新 |
| GET/POST | `/energy/readings` | 读数列表、录入读数 |
| GET | `/energy/summary` | 能耗汇总 |
| GET | `/energy/trends` | 能耗趋势 |

## 9. 能耗告警接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/energy/alerts` | 告警列表 |
| GET | `/energy/alerts/{id}` | 告警详情 |
| POST | `/energy/alerts/{id}/handle` | 处理告警 |
| POST | `/energy/alerts/{id}/resolve` | 关闭告警 |
| POST | `/energy/alerts/{id}/ignore` | 忽略告警 |

## 10. 安全巡检接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/inspections` | 巡检列表、创建 |
| GET/PATCH | `/inspections/{id}` | 巡检详情、更新 |
| POST | `/inspections/{id}/items` | 新增巡检项 |
| PATCH | `/inspection-items/{id}` | 更新巡检项 |
| POST | `/inspections/{id}/complete` | 完成巡检 |

## 11. 通知公告接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/notices` | 公告列表、创建 |
| GET/PATCH/DELETE | `/notices/{id}` | 公告详情、更新、删除 |
| POST | `/notices/{id}/publish` | 发布公告 |
| POST | `/notices/{id}/revoke` | 撤回公告 |

## 12. 通知中心接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/notifications` | 我的通知 |
| GET | `/notifications/unread-count` | 未读数量 |
| POST | `/notifications/{id}/read` | 标记已读 |
| POST | `/notifications/read-all` | 全部已读 |

## 13. 数据分析接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/analytics/repairs` | 报修分析 |
| GET | `/analytics/work-orders` | 工单效率分析 |
| GET | `/analytics/energy` | 能耗分析 |
| GET | `/analytics/occupancy` | 入住率分析 |
| GET | `/analytics/inspections` | 巡检分析 |

## 14. 用户角色权限接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET/POST | `/system/users` | 用户列表、创建 |
| GET/PATCH/DELETE | `/system/users/{id}` | 用户详情、更新、删除 |
| POST | `/system/users/{id}/roles` | 分配角色 |
| GET/POST | `/system/roles` | 角色列表、创建 |
| GET/PATCH/DELETE | `/system/roles/{id}` | 角色详情、更新、删除 |
| POST | `/system/roles/{id}/permissions` | 分配权限 |
| GET | `/system/permissions` | 权限列表 |

## 15. 审计日志接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/system/audit-logs` | 审计日志列表 |
| GET | `/system/audit-logs/{id}` | 审计日志详情 |

## 写操作要求

- 后端写操作必须校验权限、数据范围和状态流转。
- 写操作成功后记录必要审计日志。
- 前端只有收到成功响应后才更新本地状态。
- 失败时展示后端真实错误信息。
