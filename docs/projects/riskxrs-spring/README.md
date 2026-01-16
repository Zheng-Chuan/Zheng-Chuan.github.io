# XRS 微服务平台

基于 Spring Boot 微服务架构构建的综合金融报表系统,专为高性能数据处理、实时消息传递和可扩展部署而设计。

## 项目概述

XRS (eXtensible Reporting System, 可扩展报表系统) 是一个多模块微服务平台,负责处理金融数据的录入、处理、分发和报表生成。系统采用清晰的关注点分离设计,由三个独立服务组成。

## 架构设计

```
XRS-Spring/
├── xrs-entry/          # 数据录入服务
├── xrs-publish/        # 数据发布服务
├── xrs-report/         # 报表持久化服务
├── pom.xml             # 父级 POM
├── docker-compose.yml  # 基础设施编排
└── README.md
```

### 微服务模块

#### 1. **xrs-entry** - 数据录入服务
- **端口**: 8081
- **职责**: 数据录入与验证
- **核心功能**:
  - 数据录入 RESTful API
  - 输入验证与数据清洗
  - 数据转换与标准化
  - 与下游服务集成

#### 2. **xrs-publish** - 数据发布服务
- **端口**: 8082
- **职责**: 实时数据分发
- **核心功能**:
  - 基于 Kafka 的事件流
  - Redis 缓存实现高性能访问
  - 消息路由与转换
  - 异步处理

#### 3. **xrs-report** - 报表服务
- **端口**: 8080
- **职责**: 数据持久化与报表生成
- **核心功能**:
  - MySQL 数据库集成
  - 持仓与证券管理
  - 数据查询 RESTful API
  - OpenAPI 文档 (Swagger UI)
  - Redis 缓存层
  - 全面的测试覆盖

## 技术栈

| 组件 | 技术 | 版本 |
|------|------|------|
| 编程语言 | Java | 21 |
| 框架 | Spring Boot | 3.2.4 |
| 构建工具 | Maven | 3.x |
| 数据库 | MySQL | 8.x |
| 缓存 | Redis | 7.x |
| 消息队列 | Kafka | 3.6.1 |
| Redis 客户端 | Redisson | 3.24.3 |
| API 文档 | SpringDoc OpenAPI | 2.3.0 |
| 容器化 | Docker | Latest |

## 数据模型

### Position (金融持仓)
```java
- pid: Long (主键)
- uid: String (用户ID)
- secId: String (证券ID)
- value: Double (持仓价值)
- isReady: Boolean (就绪状态)
- isComponent: Boolean (组件标识)
```

### Security (金融证券)
```java
- secId: String (主键)
- name: String (证券名称)
- type: String (证券类型)
- price: Double (当前价格)
- isActive: Boolean (活跃状态)
- code: String (证券代码)
- issueDate: LocalDate (发行日期)
- expiryDate: LocalDate (到期日期)
- isComposite: Boolean (组合标识)
- parentId: String (父证券ID)
```

## 快速开始

### 前置要求
- Java 21 或更高版本
- Maven 3.6+
- Docker & Docker Compose
- Git

### 构建所有模块

```bash
# 克隆仓库
git clone https://github.com/Zheng-Chuan/XRS-Spring.git
cd XRS-Spring

# 构建所有模块
mvn clean install

# 构建特定模块
cd xrs-report
mvn clean package
```

### 使用 Docker Compose 运行

```bash
# 启动所有基础设施和服务
docker-compose up -d

# 查看日志
docker-compose logs -f

# 停止所有服务
docker-compose down
```

### 单独运行服务

```bash
# 运行 xrs-entry
cd xrs-entry
mvn spring-boot:run

# 运行 xrs-publish
cd xrs-publish
mvn spring-boot:run

# 运行 xrs-report
cd xrs-report
mvn spring-boot:run
```

## API 文档

每个服务都通过 Swagger UI 提供交互式 API 文档:

- **xrs-entry**: http://localhost:8081/swagger-ui.html
- **xrs-publish**: http://localhost:8082/swagger-ui.html
- **xrs-report**: http://localhost:8080/swagger-ui.html

### 核心接口 (xrs-report)

#### Position API
- `GET /api/positions` - 获取所有持仓
- `GET /api/positions/{pid}` - 根据ID获取持仓
- `GET /api/positions/user/{uid}` - 获取用户持仓
- `GET /api/positions/security/{secId}` - 根据证券获取持仓
- `POST /api/positions` - 创建新持仓
- `PUT /api/positions/{pid}` - 更新持仓
- `DELETE /api/positions/{pid}` - 删除持仓

#### Security API
- `GET /api/securities` - 获取所有证券
- `GET /api/securities/{secId}` - 根据ID获取证券
- `GET /api/securities/code/{code}` - 根据代码获取证券
- `GET /api/securities/type/{type}` - 根据类型获取证券
- `GET /api/securities/active` - 获取活跃证券
- `GET /api/securities/composite` - 获取组合证券
- `POST /api/securities` - 创建新证券
- `PUT /api/securities/{secId}` - 更新证券
- `DELETE /api/securities/{secId}` - 删除证券

## 配置说明

### 环境变量

```bash
# 数据库配置
SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/xrsDB
SPRING_DATASOURCE_USERNAME=root
SPRING_DATASOURCE_PASSWORD=123456

# Redis 配置
REDIS_SERVER_IP=redis
REDIS_SERVER_PORT=6379

# Kafka 配置
SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka1:9092
```

### 应用端口
- xrs-entry: 8081
- xrs-publish: 8082
- xrs-report: 8080
- MySQL: 3306
- Redis: 6379
- Kafka: 9092
- Zookeeper: 2181

## 测试

```bash
# 运行所有测试
mvn test

# 运行特定模块测试
cd xrs-report
mvn test

# 运行测试并生成覆盖率报告
mvn clean test jacoco:report

# 查看覆盖率报告
open xrs-report/target/site/jacoco/index.html
```

## 开发工作流

### 代码格式化
```bash
# 检查代码格式
mvn spotless:check

# 应用代码格式化
mvn spotless:apply
```

### 依赖分析
```bash
# 分析依赖
mvn dependency:analyze

# 显示依赖树
mvn dependency:tree
```

## 项目结构

### xrs-entry
```
xrs-entry/
├── src/main/java/com/example/xrsentry/
│   ├── controller/     # REST 控制器
│   ├── service/        # 业务逻辑
│   ├── config/         # 配置类
│   └── XrsEntryApplication.java
└── pom.xml
```

### xrs-publish
```
xrs-publish/
├── src/main/java/com/example/xrspublish/
│   ├── service/        # Kafka & Redis 服务
│   ├── config/         # Kafka & Redis 配置
│   └── XrsPublishApplication.java
└── pom.xml
```

### xrs-report
```
xrs-report/
├── src/main/java/com/example/xrsreport/
│   ├── controller/     # REST 控制器
│   ├── service/        # 业务逻辑
│   ├── repository/     # JPA 仓储
│   ├── entity/         # 领域实体
│   ├── config/         # 配置
│   └── XrsReportApplication.java
├── src/test/java/      # 全面测试
└── pom.xml
```

## 核心特性

### 事件驱动架构
- 基于 Kafka 的消息传递实现松耦合
- 异步事件处理
- 可扩展的消息分发

### 高性能缓存
- Redis 集成实现快速数据访问
- Redisson 客户端提供高级功能
- Cache-aside 模式实现

### 全面测试
- 所有层的单元测试
- Redis & Kafka 集成测试
- 使用 H2 内存数据库的仓储测试
- 使用 MockMvc 的控制器测试
- 高测试覆盖率 (>80%)

### API 文档
- 自动生成 OpenAPI 3.0 规范
- 交互式 Swagger UI
- 请求/响应示例

### 生产就绪
- Docker 容器化
- 健康检查与监控
- 结构化日志
- 错误处理
- 配置外部化

## 部署

### Docker Compose 服务
- **mysql**: MySQL 8.0 数据库
- **redis**: Redis 7.2 缓存服务器
- **zookeeper**: Kafka 协调服务
- **kafka1**: Kafka 消息代理
- **xrs-entry**: 录入服务
- **xrs-publish**: 发布服务
- **xrs-report**: 报表服务

### 服务扩展

```bash
# 将 xrs-report 扩展到 3 个实例
docker-compose up -d --scale xrs-report=3

# 将 xrs-publish 扩展到 2 个实例
docker-compose up -d --scale xrs-publish=2
```

## 监控与健康检查

所有服务都暴露 Spring Boot Actuator 端点:

```bash
# 健康检查
curl http://localhost:8080/actuator/health

# 指标
curl http://localhost:8080/actuator/metrics

# 信息
curl http://localhost:8080/actuator/info
```

## 故障排查

### 常见问题

1. **端口已被占用**
   ```bash
   # 检查端口使用情况
   lsof -i :8080
   # 终止进程
   kill -9 <PID>
   ```

2. **数据库连接失败**
   ```bash
   # 检查 MySQL 容器
   docker-compose logs mysql
   # 重启 MySQL
   docker-compose restart mysql
   ```

3. **Kafka 连接超时**
   ```bash
   # 检查 Kafka 日志
   docker-compose logs kafka1
   # 重启 Kafka
   docker-compose restart kafka1 zookeeper
   ```

## 贡献指南

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/amazing-feature`)
3. 提交更改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

### 代码规范
- 遵循 Google Java 代码风格指南
- 使用 Spotless 进行格式化
- 编写全面的测试
- 为公共 API 编写文档

## 路线图

- [ ] 添加认证与授权 (Spring Security)
- [ ] 实现分布式追踪 (Zipkin/Jaeger)
- [ ] 添加熔断器模式 (Resilience4j)
- [ ] 实现 API 网关 (Spring Cloud Gateway)
- [ ] 添加服务发现 (Eureka/Consul)
- [ ] Kubernetes 部署清单
- [ ] GraphQL API 支持
- [ ] 实时 WebSocket 通知

## 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件

## 联系方式

如有问题或需要支持,请在 GitHub 上提交 issue。

---

**使用 Spring Boot 和现代微服务架构精心打造 ❤️**
