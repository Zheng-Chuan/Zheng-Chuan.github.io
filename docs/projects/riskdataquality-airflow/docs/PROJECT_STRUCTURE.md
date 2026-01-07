# 项目结构说明

## 目录结构

```
RiskDataQuality-Airflow/
├── dags/                       # Airflow DAG 文件目录
│   ├── __init__.py
│   ├── stock_data_pipeline.py  # 股票数据采集 DAG
│   └── utils/                  # DAG 工具函数
│       ├── __init__.py
│       ├── constants.py        # 常量配置
│       └── helpers.py          # 辅助函数
│
├── hooks/                      # 自定义 Hooks
│   └── __init__.py
│
├── operators/                  # 自定义 Operators
│   └── __init__.py
│
├── sensors/                    # 自定义 Sensors
│   └── __init__.py
│
├── plugins/                    # Airflow 插件
│
├── tests/                      # 测试文件
│   ├── unit/                   # 单元测试
│   └── integration/            # 集成测试
│
├── config/                     # 配置文件
│   └── connections/            # 连接配置
│
├── scripts/                    # 脚本文件
│   ├── start.sh               # 启动脚本
│   └── stop.sh                # 停止脚本
│
├── logs/                       # Airflow 日志（gitignore）
├── docs/                       # 项目文档
│
├── docker-compose.yml          # Docker Compose 配置
├── Dockerfile                  # Docker 镜像配置
├── .env                        # 环境变量
├── requirements.txt            # Python 依赖
├── .gitignore                  # Git 忽略文件
└── README.md                   # 项目说明
```

## 目录说明

### dags/
存放所有 Airflow DAG 文件。每个 DAG 应该是一个独立的 Python 文件。

- **utils/**: DAG 相关的工具函数和常量
  - `constants.py`: 项目常量配置
  - `helpers.py`: 通用辅助函数

### hooks/
自定义 Airflow Hooks，用于管理外部系统连接（如数据库、API等）。

### operators/
自定义 Airflow Operators，封装特定的业务逻辑。

### sensors/
自定义 Airflow Sensors，用于监控外部事件或条件。

### plugins/
Airflow 插件目录，可以注册自定义的 Hooks、Operators、Sensors 等。

### tests/
测试文件目录：
- **unit/**: 单元测试
- **integration/**: 集成测试

### config/
配置文件目录：
- **connections/**: 数据库连接配置

### scripts/
项目脚本：
- `start.sh`: 启动 Docker Compose 服务
- `stop.sh`: 停止 Docker Compose 服务

### docs/
项目文档目录

## 最佳实践

1. **代码组织**
   - 每个 DAG 一个文件
   - 复杂逻辑提取到 utils/
   - 可复用组件放到 hooks/operators/sensors/

2. **命名规范**
   - DAG ID: 使用下划线分隔，如 `stock_data_pipeline`
   - 文件名: 与 DAG ID 一致
   - 函数名: 使用动词开头，如 `download_stock_data`

3. **配置管理**
   - 敏感信息使用 Airflow Connections
   - 可变配置使用 Airflow Variables
   - 常量配置放在 constants.py

4. **测试**
   - 每个 DAG 都应有对应的测试
   - 测试 DAG 加载、结构、任务逻辑
