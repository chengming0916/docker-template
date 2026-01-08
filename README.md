

# Docker 模板项目

这是一个通用的 Docker 模板项目，旨在帮助开发者快速构建和部署基于 Docker 的应用程序。该项目提供了多种常用服务的预配置 Docker 环境，方便用户根据实际需求进行选择和定制。

## 项目简介

本项目整合了多种流行的开源服务，包括消息队列、监控系统、数据库、AI 推理引擎和流媒体服务器等。通过 Docker Compose 进行统一编排，开发者可以快速搭建完整的开发和部署环境。

## 主要服务

项目包含了以下服务的 Docker 配置模板：

| 服务 | 描述 | 端口 |
|------|------|------|
| **EMQX** | 企业级 MQTT 消息 broker，支持物联网设备通信 | 1883, 8083, 8883, 18083 |
| **MySQL** | 关系型数据库服务器 | 3306 |
| **Redis** | 高性能内存键值数据库 | 6379 |
| **Ollama** | 本地大语言模型推理服务 | 11434 |
| **ZLMediaKit** | 流媒体服务器，支持 RTMP/HLS/WebRTC | - |
| **监控系统** | 包含 Prometheus、Grafana、Loki、cAdvisor 等 | - |

## 目录结构

```
docker-template/
├── emqx/              # EMQX 消息 broker 配置
│   ├── config/        # 配置文件目录
│   │   ├── acl.conf
│   │   ├── base.hocon
│   │   ├── emqx.conf
│   │   └── certs/     # SSL 证书目录
│   │       ├── cacert.pem
│   │       ├── cert.pem
│   │       └── ...
│   └── docker-compose.yml
│
├── monitoring/        # 监控系统配置
│   ├── prometheus/    # 指标收集和存储
│   ├── grafana/       # 可视化面板
│   ├── loki/          # 日志聚合
│   ├── alloy/         # 日志收集代理
│   ├── promtail/      # 日志采集
│   ├── cadvisor/      # 容器监控
│   ├── node-exporter/ # 主机指标采集
│   ├── mysql-exporter/ # MySQL 指标采集
│   ├── postgres-exporter/ # PostgreSQL 指标采集
│   ├── redis-exporter/ # Redis 指标采集
│   └── docker-compose.yml
│
├── mysql/             # MySQL 数据库配置
│   ├── config/        # 配置文件目录
│   └── docker-compose.yml
│
├── ollama/            # Ollama AI 推理服务
│   └── docker-compose.yml
│
├── redis/             # Redis 缓存服务
│   ├── config/        # 配置文件目录
│   └── docker-compose.yml
│
├── zlmediakit/        # ZLMediaKit 流媒体服务
│   ├── config/        # 配置文件目录
│   └── docker-compose.yml
│
├── LICENSE            # MIT 许可证文件
└── README.md          # 项目说明文档
```

## 快速开始

### 前置条件

- Docker Engine 20.10+
- Docker Compose V2
- 至少 2GB 可用内存（根据选择的服务而定）

### 启动单个服务

进入对应服务的目录并启动：

```bash
# 启动 MySQL
cd mysql
docker-compose up -d

# 启动 Redis
cd redis
docker-compose up -d

# 启动 EMQX
cd emqx
docker-compose up -d

# 启动监控系统
cd monitoring
docker-compose up -d

# 启动 Ollama
cd ollama
docker-compose up -d

# 启动 ZLMediaKit
cd zlmediakit
docker-compose up -d
```

### 启动所有服务

项目支持通过根目录的组合配置一次性启动所有服务。请根据实际需求修改各服务的配置后，使用以下命令：

```bash
docker-compose up -d
```

### 停止服务

```bash
docker-compose down
```

## 服务配置说明

### EMQX 配置

EMQX 是开源的物联网消息中间件，配置文件位于 `emqx/config/` 目录：

- `emqx.conf` - 主配置文件
- `acl.conf` - 访问控制列表配置
- `base.hocon` - 基础配置（Erlang 配置格式）

详细的配置示例可在 `emqx/config/examples/` 目录下找到，包括集群配置、SSL/TLS 配置、网关配置等。

### 监控系统配置

监控堆栈包含以下组件：

| 组件 | 功能 |
|------|------|
| **Prometheus** | 时序指标数据库 |
| **Grafana** | 指标可视化平台 |
| **Loki** | 日志聚合系统 |
| **Alloy/Promtail** | 日志采集器 |
| **cAdvisor** | 容器资源使用监控 |
| **Node Exporter** | 主机系统指标 |

各组件的配置文件位于 `monitoring/` 目录下对应的子目录中。

### 数据库配置

- **MySQL**: 主配置在 `mysql/config/conf.d/my.cnf`
- **Redis**: 主配置在 `redis/config/redis.conf`

## 自定义配置

### 修改端口映射

编辑各服务目录下的 `docker-compose.yml` 文件，修改 `ports` 部分：

```yaml
services:
  mysql:
    image: mysql:8.0
    ports:
      - "3307:3306"  # 修改宿主端口
```

### 环境变量配置

可以在 `docker-compose.yml` 中添加或修改 `environment` 部分来配置环境变量。

### 添加自定义服务

1. 在根目录创建新目录
2. 添加 `docker-compose.yml` 文件
3. 按需添加配置文件

## 数据持久化

各服务的数据默认使用 Docker volumes 进行持久化。如需使用宿主机目录，请修改 `docker-compose.yml` 中的 `volumes` 挂载配置。

## 安全注意事项

1. **证书管理**: EMQX 的 SSL 证书位于 `emqx/config/certs/` 目录，生产环境请使用正规 CA 签发的证书
2. **密码配置**: 请修改默认密码，特别是数据库和监控系统的访问凭证
3. **网络隔离**: 建议在生产环境使用自定义 Docker 网络进行服务隔离

## 常见问题

**Q: 如何查看服务日志？**
A: 使用 `docker-compose logs -f` 命令查看实时日志。

**Q: 如何升级服务版本？**
A: 修改 `docker-compose.yml` 中的镜像标签，然后执行 `docker-compose up -d`。

**Q: 多个服务如何通信？**
A: 使用 Docker Compose 默认的 bridge 网络，服务间可通过服务名称进行 DNS 解析。

## 许可证

本项目基于 MIT 许可证开源，详情请查看 [LICENSE](LICENSE) 文件。

## 贡献指南

欢迎提交 Issue 和 Pull Request 来完善本项目。在提交前，请确保：

1. 代码格式符合项目规范
2. 添加必要的文档说明
3. 测试通过所有服务的基本功能