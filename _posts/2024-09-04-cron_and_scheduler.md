---
layout: post
title:  "Cron and Task Scheduler"
date:   2024-09-04
categories: go_big
---

### Background
程序设计里，依据时间或者依赖其他任务、事件触发执行命令和脚本，是开发很重要的一部分。本文以类 Unix 系统最基础的 Crontab 和生产级任务调度器 Airflow 为例，整体梳理下任务调度这件事。

### Cron
Cron 是类 Unix 操作系统中一款基于时间的任务管理工具，可以通过 cron 在固定的时间、日期、间隔下，执行命令或运行脚本。
- check if cron is installed: `dpkg -l cron`
- install cron: `apt-get install cron`
- verify the status of cron: `systemctl status cron` or `service cron status`
- start/stop cron service: `systemctl start/stop cron` or `service cron start/stop`
#### Use Cases
Check Crontab use case, make sure you go through `man crontab`
- test your command line to schedule: `echo "Hello World manually at $(date)" >> $HOME/greetings_manual.txt`
- check result: `tail ~/greetings_manual.txt`
- install new crontab job, and choose vim editor: `crontab -e`
- add a line `* * * * * echo "Hello World automatically at $(date)" >> $HOME/greetings.txt"` to the end of the crontab file, save and exit the editor
- check result: `tail ~/greetings.txt`
- list all crontab jobs: `crontab -l`
- remove all crontab jobs created by current user: `crontab -r`
#### Syntax
`* * * * * [command to execute]`
- First * stands for representing minutes [0-59];
- Second * stands for representing hour[0-23];
- Third * stands for representing day [0-31];
- Fourth * stands for representing month[0-12];
- Fifth * stands for representing a day of the week[0-6];

You can check your cron schedule expressions at: [Cronitor](https://crontab.guru/).

### Apache Airflow
[Airflow](https://airflow.apache.org/) 是一款功能强大的开源工作流调度工具，除了内置的如bash、python、email等 [Core Operators](https://airflow.apache.org/docs/apache-airflow/stable/operators-and-hooks-ref.html) 以外，还集成了大量第三方平台应用/软件的 [Community Operators](https://airflow.apache.org/docs/apache-airflow-providers/operators-and-hooks-ref/index.html)，包含 Apache Software Foundation, Amazon Web Services, Microsoft Azure, Google, etc 相关服务的调度，并且能够监控任务的状态。Airflow 服务器可以部署在单机，也可以根据需求扩展至多节点部署。

#### installation
Only `pip` installation is currently officially supported.

```bash
AIRFLOW_VERSION=2.10.0

PYTHON_VERSION="$(python3 --version | cut -d " " -f 2 | cut -d "." -f 1-2)"

CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"

pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
```

#### init airflow database
```bash
airflow db init
```

#### create a user
```bash
airflow users create \
--username thekingofcool \
--firstname bug \
--lastname hunter \
--role Admin \
--email sayhi@thekingof.cool
```

#### start airflow scheduler and web server
```bash
airflow scheduler
airflow webserver --port 8080
```

visit [localhost:8080](http://localhost:8080) to check out the web page.

#### airflow dag file
在 Airflow 配置文件中查看 dag 文件位置:
```bash
cat ~/airflow/airflow.cfg | grep dags_folder
```
在该文件目录下编辑 dag 文件，重启 airflow scheduler。

Note:
- Recommend to use Postgres or MySQL as [metadata DB](https://airflow.apache.org/docs/apache-airflow/2.10.0/howto/set-up-database.html) in production;
- Do not use the [SequentialExecutor](https://airflow.apache.org/docs/apache-airflow/2.10.0/core-concepts/executor/index.html) in production.

To be continued...