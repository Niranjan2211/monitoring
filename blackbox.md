How to install BlackBox 

```
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.14.0/blackbox_exporter-0.14.0.linux-amd64.tar.gz
tar xvzf blackbox_exporter-0.14.0.linux-amd64.tar.gz

$ cd blackbox_exporter-0.14.0.linux-amd64
$ ./blackbox_exporter -h

$ sudo mv blackbox_exporter /usr/local/bin
$ sudo mkdir -p /etc/blackbox
$ sudo mv blackbox.yml /etc/blackbox
$ sudo useradd -rs /bin/false blackbox
$ sudo chown blackbox:blackbox /usr/local/bin/blackbox_exporter
$ sudo chown -R blackbox:blackbox /etc/blackbox/*
$ cd /lib/systemd/system
$ sudo touch blackbox.service

sudo nano blackbox.service

[Unit]
Description=Blackbox Exporter Service
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=blackbox
Group=blackbox
ExecStart=/usr/local/bin/blackbox_exporter \
  --config.file=/etc/blackbox/blackbox.yml \
  --web.listen-address=":9115"

Restart=always

[Install]
WantedBy=multi-user.target

$ sudo systemctl daemon-reload
$ sudo systemctl enable blackbox.service
$ sudo systemctl start blackbox.service

$ sudo nano /etc/prometheus/prometheus.yml

global:
  scrape_interval:     15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090', 'localhost:9115']
    
    

scrape_configs:

...

  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_prometheus] 
    static_configs:
      - targets:
        - https://127.0.0.1:1234    # Target to probe with https.
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 127.0.0.1:9115  # The blackbox exporter's real hostname:port.







```
