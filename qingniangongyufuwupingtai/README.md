# 青年公寓服务平台

面向青年公寓租赁场景的 Web 管理系统，提供房屋发布与浏览、预约看房、投诉反馈、博客社区等功能，支持管理员、商家、普通用户三类角色协同使用。

## 功能概览

### 用户端（前台）

- 房屋浏览、搜索、收藏与留言
- 在线预约看房、订单确认
- 房屋投诉提交与跟踪
- 博客发布、收藏与留言互动
- 用户/商家注册登录、个人中心、账户充值
- 在线客服聊天

### 管理端（后台）

- 用户、商家、管理员账号管理
- 房屋信息维护（类型、朝向、上下架等）
- 预约看房订单审核
- 房屋投诉处理
- 博客及互动内容管理
- 数据字典与轮播图配置
- 数据统计（ECharts）

## 技术栈

| 层级 | 技术 |
|------|------|
| 后端 | Spring Boot 2.2.2、MyBatis-Plus、Apache Shiro |
| 数据库 | MySQL 5.7+ |
| 管理后台 | Vue 2、Element UI、Vue Router、Axios、ECharts |
| 用户前台 | HTML + Layui + Vue + jQuery |
| 工具库 | Hutool、FastJSON、Apache POI、百度 AI SDK |

## 项目结构

```
qingniangongyufuwupingtai/
├── pom.xml                              # Maven 配置
├── src/main/java/com/
│   ├── controller/                      # REST 接口层
│   ├── service/                         # 业务逻辑层
│   ├── dao/                             # 数据访问层
│   ├── entity/                          # 实体类
│   └── qingniangongyufuwupingtaiApplication.java  # 启动类
├── src/main/resources/
│   ├── application.yml                  # 应用配置
│   ├── mapper/                          # MyBatis XML 映射
│   ├── admin/admin/                     # 管理后台 Vue 源码
│   │   ├── src/                         # 前端源码
│   │   └── dist/                        # 构建产物（已包含）
│   └── front/front/                     # 用户前台静态页面
└── upload/                              # 文件上传目录
```

## 环境要求

- JDK 1.8+
- Maven 3.6+
- MySQL 5.7+（或 8.x）
- Node.js 14+（仅修改管理后台前端时需要）

## 快速开始

### 1. 准备数据库

创建数据库并导入表结构与初始数据（数据库名需与配置一致）：

```sql
CREATE DATABASE qingniangongyufuwupingtai DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

> 若项目附带 `.sql` 脚本，请执行该脚本完成建表与初始化。仓库内未包含 SQL 文件时，请从项目提供方获取数据库脚本。

### 2. 修改配置

编辑 `src/main/resources/application.yml`，按本地环境修改数据库连接信息：

```yaml
spring:
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/qingniangongyufuwupingtai?useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8
    username: root
    password: 你的密码
```

### 3. 启动后端

在项目根目录执行：

```bash
mvn spring-boot:run
```

或在 IDE 中运行 `com.qingniangongyufuwupingtaiApplication`。

启动成功后默认监听 `8080` 端口，应用上下文路径为 `/qingniangongyufuwupingtai`。Windows 环境下启动时会自动打开管理后台页面。

### 4. 构建管理后台（可选）

管理后台已包含 `dist` 构建产物，一般无需重新构建。若修改了 `src/main/resources/admin/admin` 下的源码，需重新打包：

```bash
cd src/main/resources/admin/admin
npm install
npm run build
```

## 访问地址

| 入口 | 地址 |
|------|------|
| 管理后台 | http://localhost:8080/qingniangongyufuwupingtai/admin/dist/index.html |
| 用户前台 | http://localhost:8080/qingniangongyufuwupingtai/front/index.html |

## 角色说明

| 角色 | 说明 |
|------|------|
| 管理员 | 通过管理后台登录，负责全平台数据管理与审核 |
| 商家 | 发布与管理房屋信息，处理预约与投诉 |
| 用户 | 浏览房屋、预约看房、收藏留言、发布博客 |

各角色可在前台注册，管理员账号通过 `users` 表维护。

## 核心业务模块

| 模块 | 数据表 | 说明 |
|------|--------|------|
| 房屋 | `fangwu` | 房屋基本信息、价格、照片、上下架 |
| 预约看房 | `fangwu_order` | 用户预约申请与审核状态 |
| 房屋收藏 | `fangwu_collection` | 用户收藏记录 |
| 房屋留言 | `fangwu_liuyan` | 房屋详情页留言 |
| 房屋投诉 | `fangwu_tousu` | 投诉类型与处理状态 |
| 博客 | `boke` | 社区文章发布 |
| 博客收藏/留言 | `boke_collection` / `boke_liuyan` | 博客互动 |
| 用户/商家 | `yonghu` / `shangjia` | 前台注册用户与商家 |
| 字典/配置 | `dictionary` / `config` | 枚举项与轮播图等系统配置 |

## 接口说明

后端以 RESTful 风格提供 JSON 接口，统一前缀为 `/qingniangongyufuwupingtai`。主要控制器包括：

- `FangwuController` — 房屋
- `FangwuOrderController` — 预约看房
- `FangwuTousuController` — 房屋投诉
- `BokeController` — 博客
- `YonghuController` / `ShangjiaController` — 用户与商家
- `UsersController` — 管理员登录
- `FileController` — 文件上传

接口鉴权基于 Token 机制（`token` 表 + Shiro 拦截）。

## 注意事项

- 上传文件默认保存在项目根目录 `upload/` 及 `static/` 路径，请确保目录可写。
- `application.yml` 中配置了较大的文件上传限制（1000MB），可按需调整。
- 生产环境部署前请修改默认数据库密码，并关闭 SQL 调试日志（`logging.level.com.dao`）。

## 预览

![系统预览](https://github.com/user-attachments/assets/5e16f9ba-13cb-418b-8f9a-d143e81daf42)

## 许可证

本项目仅供学习与交流使用。
