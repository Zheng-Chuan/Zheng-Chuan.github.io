# 开发指南

## 环境准备

### 1. 安装依赖

```bash
# 激活 conda 环境
conda activate AirFlow

# 安装项目依赖
pip install -r requirements.txt
```

### 2. 配置数据库连接

在 Airflow Web UI 中配置 MySQL 连接：

```bash
# 方式一：通过 Web UI
Admin → Connections → Add Connection

# 方式二：通过命令行
docker-compose exec airflow-webserver airflow connections add 'MySQL' \
  --conn-type 'mysql' \
  --conn-host 'mysql' \
  --conn-login 'airflow' \
  --conn-password 'airflow' \
  --conn-schema 'DataFlow_DB' \
  --conn-port 3306
```

### 3. 配置 Airflow Variables

```bash
# 设置股票代码列表
airflow variables set stock_symbols '["AAPL", "MSFT", "GOOGL", "AMZN"]'
```

## 开发流程

### 1. 创建新的 DAG

1. 在 `dags/` 目录下创建新的 Python 文件
2. 导入必要的模块和工具函数
3. 定义 DAG 和任务
4. 设置任务依赖关系

示例：

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime, timedelta
from utils.constants import DEFAULT_STOCK_SYMBOLS

default_args = {
    'owner': 'airflow',
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

with DAG(
    dag_id='my_new_dag',
    default_args=default_args,
    schedule_interval=timedelta(days=1),
    start_date=datetime(2024, 1, 1),
    catchup=False,
) as dag:
    task = PythonOperator(
        task_id='my_task',
        python_callable=my_function,
    )
```

### 2. 创建自定义 Hook

在 `hooks/` 目录下创建新的 Hook：

```python
from airflow.hooks.base import BaseHook

class MyCustomHook(BaseHook):
    def __init__(self, conn_id: str):
        self.conn_id = conn_id
    
    def get_connection(self):
        return BaseHook.get_connection(self.conn_id)
```

### 3. 创建自定义 Operator

在 `operators/` 目录下创建新的 Operator：

```python
from airflow.models import BaseOperator

class MyCustomOperator(BaseOperator):
    def __init__(self, my_param, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.my_param = my_param
    
    def execute(self, context):
        # 实现业务逻辑
        pass
```

### 4. 添加工具函数

在 `dags/utils/` 目录下添加通用函数：

- `constants.py`: 添加常量配置
- `helpers.py`: 添加辅助函数

## 测试

### 运行单元测试

```bash
# 运行所有测试
python -m pytest tests/unit/ -v

# 运行特定测试文件
python -m pytest tests/unit/test_stock_data_pipeline.py -v

# 查看测试覆盖率
python -m pytest tests/unit/ --cov=dags --cov-report=html
```

### 测试 DAG

```bash
# 测试 DAG 语法
python dags/stock_data_pipeline.py

# 测试 DAG 加载
airflow dags list

# 测试单个任务
airflow tasks test stock_data_pipeline download_stock_data 2024-01-01
```

## 调试

### 查看日志

```bash
# Docker 环境
docker-compose logs -f airflow-webserver
docker-compose logs -f airflow-scheduler

# 本地环境
tail -f logs/scheduler/latest/*.log
```

### 进入容器调试

```bash
# 进入 webserver 容器
docker-compose exec airflow-webserver bash

# 进入 scheduler 容器
docker-compose exec airflow-scheduler bash

# 在容器内执行 Airflow 命令
airflow dags list
airflow tasks list stock_data_pipeline
```

## 代码规范

### Python 代码风格

- 遵循 PEP 8 规范
- 使用类型注解
- 添加文档字符串
- 函数名使用动词开头

### 提交规范

```bash
# 提交格式
<type>: <subject>

# 类型
feat: 新功能
fix: 修复 bug
docs: 文档更新
style: 代码格式调整
refactor: 重构
test: 测试相关
chore: 构建/工具链相关
```

## 常见问题

### DAG 未显示

1. 检查 DAG 文件语法错误
2. 查看 Airflow 日志
3. 刷新 DAG：`airflow dags list-import-errors`

### 任务执行失败

1. 查看任务日志
2. 检查数据库连接
3. 验证 Airflow Variables 配置

### 数据库连接失败

1. 检查连接配置
2. 测试连接：`airflow connections test MySQL`
3. 确认数据库服务运行正常
