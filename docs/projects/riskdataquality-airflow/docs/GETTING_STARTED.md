# 快速开始指南

## 📋 项目概述

这是一个基于 Apache Airflow 的数据质量监控项目，主要用于股票数据的自动化采集和质量监控。

**🎯 项目目标**: 通过实际业务场景，全面展示 Apache Airflow 的核心功能特性，包括自定义组件开发、任务编排、数据质量监控等。

---

## 🚀 环境要求

- Python 3.11.5
- Apache Airflow 2.10.5
- Docker & Docker Compose (推荐)
- MySQL 8.0
- PostgreSQL 13

---

## ⚡ 快速启动

### 方式一: Docker Compose 启动 (推荐)

```bash
# 1. 克隆项目
git clone <repository-url>
cd RiskDataQuality-Airflow

# 2. 一键启动所有服务
./scripts/start.sh

# 或手动启动
docker-compose up -d

# 3. 等待服务启动 (约 1-2 分钟)
docker-compose logs -f

# 4. 访问 Airflow Web UI
# http://localhost:8080
# 用户名: airflow
# 密码: airflow
```

**停止服务**:
```bash
./scripts/stop.sh
# 或
docker-compose down
```

---

### 方式二: 本地直接启动

#### 1. 创建 Conda 环境
```bash
conda create -n AirFlow python=3.11.5
conda activate AirFlow
```

#### 2. 安装依赖
```bash
pip install -r requirements.txt
```

或手动安装:
```bash
pip install apache-airflow
pip install mysql-connector-python
pip install yfinance
pip install pytest
pip install apache-airflow-providers-mysql
```

#### 3. 初始化 Airflow
```bash
# 初始化数据库
airflow db init

# 创建管理员用户
airflow users create \
    --username admin \
    --firstname Admin \
    --lastname User \
    --role Admin \
    --email admin@example.com \
    --password admin
```

#### 4. 启动服务
```bash
# 启动 web 服务器 (端口 8080)
airflow webserver --port 8080

# 新开终端窗口，启动调度器
airflow scheduler
```

---

## 🔧 数据库配置

### MySQL 配置

项目需要 MySQL 数据库支持:
- **数据库名**: `DataFlow_DB`
- **表名**: `stock_prices` (自动创建)
- **连接 ID**: `MySQL` (在 Airflow 中配置)

#### 表结构
```sql
CREATE TABLE stock_prices (
    symbol VARCHAR(10),
    date DATE,
    open FLOAT,
    high FLOAT,
    low FLOAT,
    close FLOAT,
    volume BIGINT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (symbol, date)
);
```

#### 配置 Airflow 连接

**Docker 环境** (在容器内执行):
```bash
docker-compose exec airflow-webserver airflow connections add 'MySQL' \
  --conn-type 'mysql' \
  --conn-host 'mysql' \
  --conn-login 'airflow' \
  --conn-password 'airflow' \
  --conn-schema 'DataFlow_DB' \
  --conn-port 3306
```

**本地环境**:
```bash
airflow connections add 'MySQL' \
  --conn-type 'mysql' \
  --conn-host 'localhost' \
  --conn-login 'root' \
  --conn-password 'your_password' \
  --conn-schema 'DataFlow_DB' \
  --conn-port 3306
```

或在 Airflow Web UI 中配置:
1. 访问 Admin → Connections
2. 点击 "+" 添加新连接
3. 填写连接信息

---

## 📊 访问信息

### Docker 环境
- **Airflow Web UI**: http://localhost:8080
- **用户名**: `airflow`
- **密码**: `airflow`
- **PostgreSQL** (Airflow 元数据): `localhost:5432`
- **MySQL** (业务数据): `localhost:3306`

### 数据库连接
```bash
# PostgreSQL
psql -h localhost -p 5432 -U airflow -d airflow

# MySQL
mysql -h 127.0.0.1 -P 3306 -u airflow -p
# 密码: airflow
```

---

## 🎮 常用命令

### DAG 管理
```bash
# 查看 DAG 列表
airflow dags list

# 测试 DAG 语法
airflow dags test stock_data_pipeline

# 手动触发 DAG
airflow dags trigger stock_data_pipeline

# 检查 DAG 状态
airflow dags state stock_data_pipeline $(date +"%Y-%m-%d")
```

### 任务操作
```bash
# 测试单个任务
airflow tasks test stock_data_pipeline download_stock_data $(date +"%Y-%m-%d")

# 查看任务日志
airflow tasks log stock_data_pipeline download_stock_data $(date +"%Y-%m-%d")
```

### 变量管理
```bash
# 设置股票代码变量
airflow variables set stock_symbols '["AAPL", "MSFT", "GOOGL", "AMZN"]'

# 查看变量
airflow variables get stock_symbols

# 列出所有变量
airflow variables list
```

### Docker 管理
```bash
# 查看服务状态
docker-compose ps

# 查看所有日志
docker-compose logs -f

# 查看特定服务日志
docker-compose logs -f webserver
docker-compose logs -f scheduler

# 重启服务
docker-compose restart

# 停止并删除数据卷
docker-compose down -v

# 进入容器
docker-compose exec airflow-webserver bash
docker-compose exec mysql bash
```

---

## 🧪 测试

### 运行测试
```bash
# 运行所有单元测试
python -m pytest tests/unit/ -v

# 运行集成测试
python -m pytest tests/integration/ -v

# 运行特定测试文件
python -m pytest tests/unit/test_stock_data_pipeline.py -v

# 使用测试脚本
./scripts/run_tests.sh
```

---

## ⚠️ 故障排查

### 常见问题

#### 1. Airflow 无法启动
```bash
# 检查端口占用
lsof -i :8080

# 清理并重新初始化
airflow db reset
airflow db init
```

#### 2. DAG 未显示
- 检查 DAG 文件语法: `python dags/stock_data_pipeline.py`
- 查看 Scheduler 日志: `docker-compose logs -f scheduler`
- 确认 DAG 文件在正确目录: `dags/`

#### 3. 数据库连接失败
- 检查 MySQL 服务状态: `docker-compose ps mysql`
- 验证连接配置: Airflow UI → Admin → Connections
- 测试连接: `airflow connections test MySQL`

#### 4. 依赖安装失败
```bash
# Docker 环境重新构建
docker-compose down
docker-compose build --no-cache
docker-compose up -d

# 本地环境
pip install --upgrade pip
pip install -r requirements.txt --force-reinstall
```

---

## 📚 下一步

- 📖 阅读 [项目架构设计](ARCHITECTURE.md)
- 🏗️ 查看 [项目结构说明](PROJECT_STRUCTURE.md)
- 💻 学习 [开发指南](DEVELOPMENT.md)
- 📅 了解 [开发计划](ROADMAP.md)

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request!

## 📄 许可证

MIT License
