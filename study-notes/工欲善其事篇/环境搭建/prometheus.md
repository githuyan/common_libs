### Prometheus+Grafana监控系统

**参考：**

- https://juejin.cn/post/6969764486701924383



```yaml
# docker-compose.yaml

version: '3'

networks:
    monitor:
        driver: bridge

services:
    prometheus:
        image: prom/prometheus
        container_name: prometheus
        hostname: prometheus
        # restart: always
        volumes:
            - ./conf/prometheus.yml:/etc/prometheus/prometheus.yml
            - ./conf/node_down.yml:/etc/prometheus/node_down.yml
        ports:
            - "9090:9090"
        networks:
            - monitor

    grafana:
        image: grafana/grafana
        container_name: grafana
        hostname: grafana
        # restart: always
        ports:
            - "3000:3000"
        networks:
            - monitor
    redis-exporter:
        image: oliver006/redis_exporter
        container_name: redis_exporter
        hostname: redis_exporter
        # restart: always
        ports:
            - "9121:9121"
        networks:
            - monitor
        command:
            - '--redis.addr=redis://172.17.0.1:6379'
        user: "0"
    node-exporter:
        image: quay.io/prometheus/node-exporter
        container_name: node-exporter
        hostname: node-exporter
        # restart: always
        ports:
            - "9100:9100"
        networks:
            - monitor

    cadvisor:
        image: google/cadvisor:latest
        container_name: cadvisor
        hostname: cadvisor
        # restart: always
        volumes:
            - /:/rootfs:ro
            - /var/run:/var/run:rw
            - /sys:/sys:ro
            - /var/lib/docker/:/var/lib/docker:ro
        ports:
            - "8088:8080"
        networks:
            - monitor

```

```yaml
# ./conf/prometheus.yaml
global:
  scrape_interval:     15s # 数据抓取间隔 Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # 规则评估间隔 Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# # 告警配置 Alertmanager configuration
# alerting:
#   alertmanagers:
#   - static_configs:
#     - targets: ['10.10.170.161:9093']

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - "node_down.yml"
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9090']

  - job_name: 'redis'
    static_configs:
     - targets: ['172.17.0.1:9121']
       labels:
         instance: redis

  - job_name: 'node'
    scrape_interval: 8s
    static_configs:
     - targets: ['172.17.0.1:9100']
       labels:
         instance: node

  - job_name: 'cadvisor'
    static_configs:
     - targets: ['172.17.0.1:8088']
       labels:
         instance: cadvisor
```

```yaml
# ./conf/node_down.yaml

groups:
- name: node_down
  rules:
  - alert: InstanceDown
    expr: up == 0
    for: 1m
    labels:
      user: test
    annotations:
      summary: "Instance {{ $labels.instance }} down"
      description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 1 minutes."

```

