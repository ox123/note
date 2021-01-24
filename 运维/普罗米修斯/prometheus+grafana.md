1. https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz

2. vim /etc/systemd/system/node_exporter.service

   ```
   [Unit]
   Description=Node Exporter
   Wants=network-online.target
   After=network-online.target
   
   [Service]
   User=node_exporter
   Group=node_exporter
   Type=simple
   ExecStart=/opt/pkg/node_exporter/node_exporter
   
   [Install]
   WantedBy=multi-user.target
   ```

3. systemctl daemon-reload

4. systemctl start node_exporter

5. systemctl status node_exporter

6. 开启 Node Exporter 的开机自启动了

   ```bash
   sudo systemctl enable node_exporter
   ```

### prometheus

1. wget https://github.com/prometheus/prometheus/releases/download/v2.24.1/prometheus-2.24.1.linux-amd64.tar.gz

2. vim /etc/systemd/system/prometheus.service

   ```
   [Unit]
     Description=Prometheus Monitoring
     Wants=network-online.target
     After=network-online.target
   
   [Service]
     Type=simple
     ExecStart=/opt/pkg/prometheus/prometheus \
     --config.file /opt/pkg/prometheus/prometheus.yml \
     --storage.tsdb.path /opt/pkg/prometheus/data \
     --web.console.templates=/opt/pkg/prometheus/consoles \
     --web.console.libraries=/opt/pkg/prometheus/console_libraries
     ExecReload=/bin/kill -HUP $MAINPID
   
   [Install]
     WantedBy=multi-user.target
   ```

3. systemctl daemon-reload

4. systemctl enable prometheus

5. systemctl start prometheus

### grafana

1. systemctl daemon-reload && sudo systemctl enable grafana-server && sudo systemctl start grafana-server