# Project-Deploying-System-Observability

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew update

brew install prometheus
prometheus --config.file=/usr/local/etc/prometheus.yml
brew services start prometheus

brew install node_exporter
node_exporter
brew services start node_exporter


brew install grafana
grafana-server --config=/usr/local/etc/grafana/grafana.ini
brew services start grafana
Default login: admin / admin (you’ll be prompted to change this after the first login).

brew install blackbox-exporter
blackbox_exporter --config.file=/usr/local/etc/prometheus/blackbox.yml
brew services start blackbox-exporter

Verify Blackbox Exporter:

Ensure it's running by checking http://localhost:9115/metrics in your browser.

Edit Prometheus Configuration:

Add alerting rules in the Prometheus configuration file (/usr/local/etc/prometheus.yml).


groups:
  - name: system_alerts
    rules:
      - alert: HighCPUUsage
        expr: 100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 85
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "High CPU Usage on {{ $labels.instance }}"
          description: "{{ $labels.instance }} is experiencing high CPU usage."

brew services restart prometheus
