# Weather ETL

Этот репозиторий содержит демонстрационный ETL‑конвейер, разворачиваемый через `docker-compose`. Проект собирает данные из различных источников (API, Kafka, PostgreSQL), сохраняет их в хранилище объектов MinIO и ClickHouse, а также формирует витрины данных c помощью DBT. Визуализация осуществляется в Superset, наблюдение за сервисами – в Prometheus и Grafana.

## Структура проекта

- **airflow_dockerfile/** – образы и настройки Apache Airflow.
- **bash_control/** – скрипты `up.sh` и `down.sh` для быстрого запуска и остановки всего стека.
- **clickhouse/** – инициализация баз данных ClickHouse.
- **dags/** – DAG‑и Airflow для загрузки данных из API и баз.
- **dbt_click/** – проект DBT для построения витрин в ClickHouse.
- **debezium-\***/ – конфигурации Debezium Kafka Connect, Prometheus и Grafana.
- **jupyter_dockerfile/** – образ окружения Jupyter Notebook.
- **plugins/** – вспомогательные плагины Airflow.
- **scripts/** – Python‑скрипты, выполняемые из DAG‑ов (загрузка и трансформации данных).


## Используемые технологии

- **Apache Airflow** – оркестрация ETL‑процессов.
- **Apache Spark** – обработка и загрузка данных из PostgreSQL и Kafka.
- **Kafka / Debezium** – потоковая передача изменений из PostgreSQL.
- **ClickHouse** – аналитическая СУБД для хранения витрин.
- **MinIO (S3)** – объектное хранилище для данных Data Lake.
- **DBT** – моделирование и загрузка данных в ClickHouse.
- **Superset** – визуализация данных.
- **Prometheus + Grafana** – мониторинг и дашборды.
- **Jupyter Notebook** – интерактивный анализ и тестирование кода.

## Процесс работы

1. С помощью `bash_control/up.sh` запускается `docker-compose`, разворачивая все сервисы.
2. Airflow выполняет DAG‑и из каталога `dags/`. Они обращаются к внешним API, Kafka и PostgreSQL, сохраняя данные в MinIO.
3. Скрипты из `scripts/` запускаются как задачи Airflow и обогащают данные либо загружают их в ClickHouse.
4. DBT‑проект `dbt_click` строит витрины на основе данных, лежащих в ClickHouse.
5. Superset подключается к ClickHouse и отображает подготовленные дашборды.
6. Prometheus собирает метрики, а Grafana визуализирует состояние сервисов.
7. Завершить работу можно через `bash_control/down.sh`.
