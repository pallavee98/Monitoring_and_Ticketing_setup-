# Monitoring_and_Ticketing_setup-
## Prometheus set up
- Download and Install Prometheus
- Go to the website 
```
https://prometheus.io/download/
```

- Use link to install prometheus for linux 
```
prometheus-2.54.0.linux-amd64.tar.gz
```
- Extract the tarball 
```
Tar xvfz prometheus-2.54.0.linux-amd64.tar.gz
```
- Move the Prometheus Binary to /usr/local/bin
```
cd prometheus-2.45.0.linux-amd64
```
- Move the Prometheus and promtool binaries to /usr/local/bin:
```
sudo mv prometheus /usr/local/bin/
sudo mv promtool /usr/local/bin/
```

- Move Configuration Files to /etc/prometheus
```
sudo mkdir /etc/prometheus
```
- Move the configuration files to /etc/prometheus:
```
sudo mv prometheus.yml /etc/prometheus/
sudo mv consoles /etc/prometheus/
sudo mv console_libraries /etc/prometheus/
```
## Set Up the Data Directory
- Create a directory for Prometheus data:
```
sudo mkdir /var/lib/prometheus
```
- Ensure proper permissions are set for the Prometheus user
```
sudo chown prometheus:prometheus /var/lib/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus
```
## Creating systemd service file
- Create a Prometheus Systemd Service File
```
sudo nano /etc/systemd/system/prometheus.service
```
- Add the following configuration to the file:
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file=/etc/prometheus/prometheus.yml \
    --storage.tsdb.path=/var/lib/prometheus/data \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries
Restart=always

[Install]
WantedBy=multi-user.target
```

## Reload Systemd to Apply the New Service
- Reload the systemd manager configuration to recognize the new service:
```
sudo systemctl daemon-reload
```
## Start the Prometheus Service
```
sudo systemctl start prometheus
```
- Check the status to ensure it’s running correctly:
```
sudo systemctl status prometheus
```
### Output
```
pallavee@pallavee:~$ systemctl status prometheus
● prometheus.service - Prometheus
     Loaded: loaded (/etc/systemd/system/prometheus.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2024-08-12 10:39:51 IST; 2h 19min ago
   Main PID: 1758 (prometheus)
      Tasks: 10 (limit: 9318)
     Memory: 118.6M
        CPU: 12.541s
     CGroup: /system.slice/prometheus.service
             └─1758 /usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus/data --web.console.templates=/etc/prometheus/consoles --we>

Aug 12 10:39:52 pallavee prometheus[1758]: ts=2024-08-12T05:09:52.056Z caller=main.go:1391 level=info msg="updated GOGC" old=100 new=75
Aug 12 10:39:52 pallavee prometheus[1758]: ts=2024-08-12T05:09:52.056Z caller=main.go:1402 level=info msg="Completed loading of configuration file" filename=/etc/prometheus/prometheus.yml t>
Aug 12 10:39:52 pallavee prometheus[1758]: ts=2024-08-12T05:09:52.062Z caller=main.go:1133 level=info msg="Server is ready to receive web requests."
Aug 12 10:39:52 pallavee prometheus[1758]: ts=2024-08-12T05:09:52.062Z caller=manager.go:164 level=info component="rule manager" msg="Starting rule manager..."
Aug 12 10:40:01 pallavee prometheus[1758]: ts=2024-08-12T05:10:01.411Z caller=compact.go:567 level=info component=tsdb msg="write block resulted in empty block" mint=1723399200000 maxt=1723>
Aug 12 10:40:01 pallavee prometheus[1758]: ts=2024-08-12T05:10:01.419Z caller=head.go:1355 level=info component=tsdb msg="Head GC completed" caller=truncateMemory duration=6.33163ms
Aug 12 10:40:01 pallavee prometheus[1758]: ts=2024-08-12T05:10:01.419Z caller=checkpoint.go:101 level=info component=tsdb msg="Creating checkpoint" from_segment=422 to_segment=423 mint=1723>
Aug 12 10:40:01 pallavee prometheus[1758]: ts=2024-08-12T05:10:01.437Z caller=head.go:1317 level=info component=tsdb msg="WAL checkpoint complete" first=422 last=423 duration=17.35924ms
Aug 12 12:40:02 pallavee prometheus[1758]: ts=2024-08-12T07:10:02.324Z caller=compact.go:576 level=info component=tsdb msg="write block" mint=1723435801172 maxt=1723442400000 ulid=01J52PRAF>
Aug 12 12:40:02 pallavee prometheus[1758]: ts=2024-08-12T07:10:02.330Z caller=head.go:1355 level=info component=tsdb msg="Head GC completed" caller=truncateMemory duration=3.661482ms
lines 1-20/20 (END)
```
- Enable Prometheus to Start on Boot
```
sudo systemctl enable prometheus
```
## Troubleshooting 
- View Logs: If Prometheus fails to start, you can check the logs for errors:
```
journalctl -u prometheus.service
 ```
- To access prometheus go to the  web browser and enter
```
http://localhost:9090/graph?g0.expr=&g0.tab=1&g0.display_mode=lines&g0.show_exemplars=0&g0.range_input=1h
```
## Node exporter set up 

- GO to the website
```
https://prometheus.io/download/
```
- install node exporter
```
node_exporter-1.8.2.linux-amd64.tar.gz
tar xvfz node_exporter-1.8.2 linux-amd64.tar.gz
```
- Move the Node Exporter binary to /usr/local/bin:
```
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
```
## Create a Node Exporter Systemd Service File
- Create the service file:
```
sudo nano /etc/systemd/system/node_exporter.service
```
- Add content
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```
## Start Node Exporter
```
sudo systemctl daemon-reload
sudo systemctl start node_exporter
```
- Enable Node Exporter to start on boot:
```
sudo systemctl enable node_exporter
```
- Verify the service 
```
sudo systemctl status node_exporter
```
### Output 
```
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2024-08-12 10:39:51 IST; 2h 49min ago
   Main PID: 1743 (node_exporter)
      Tasks: 7 (limit: 9318)
     Memory: 21.6M
        CPU: 27.119s
     CGroup: /system.slice/node_exporter.service
             └─1743 /usr/local/bin/node_exporter

Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=time
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=timex
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=udp_queues
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=uname
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=vmstat
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=watchdog
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=xfs
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.426Z caller=node_exporter.go:118 level=info collector=zfs
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.433Z caller=tls_config.go:313 level=info msg="Listening on" address=[::]:9100
Aug 12 10:39:51 pallavee node_exporter[1743]: ts=2024-08-12T05:09:51.433Z caller=tls_config.go:316 level=info msg="TLS is disabled." http2=false address=[::]:9100
```
## Troubleshooting 
```
journalctl -u node_exporter.service 
```
- To access node exporter go to the web browser
```
  enter  localhost: 9100
```
## ALertmanager installation and set up
- Go to the website
```
https://prometheus.io/download/
```
- Download alertmanager for linux
```
alertmanager-0.27.0.linux-amd64.tar.gz
tar xvfz alertmanager-0.27.0.linux-amd64.tar.gz
```
- Move the Alertmanager binary and files:
```
sudo mv alertmanager-0.26.0.linux-amd64/alertmanager /usr/local/bin/
sudo mv alertmanager-0.26.0.linux-amd64/amtool /usr/local/bin/
sudo mkdir /etc/alertmanager
sudo mv alertmanager-0.26.0.linux-amd64/alertmanager.yml /etc/alertmanager/
```
## Create the service file:
```
sudo nano /etc/systemd/system/alertmanager.service
```    
```
[Unit]
Description=Prometheus Alertmanager Service
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/local/bin/alertmanager \
  --config.file=/etc/alertmanager/alertmanager.yml \
  --storage.path=/var/lib/alertmanager/data

[Install]
WantedBy=multi-user.target
```
## Start service
```
sudo systemctl start alertmanager
sudo systemctl enable alertmanager
sudo systemctl status alertmanager
```
### Output 
```
pallavee@pallavee:~$ sudo nano /etc/systemd/system/alertmanager.service
pallavee@pallavee:~$ sudo systemctl status alertmanager
● alertmanager.service - Prometheus Alertmanager Service
     Loaded: loaded (/etc/systemd/system/alertmanager.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2024-08-12 10:39:51 IST; 2h 54min ago
   Main PID: 1730 (alertmanager)
      Tasks: 9 (limit: 9318)
     Memory: 36.3M
        CPU: 11.076s
     CGroup: /system.slice/alertmanager.service
             └─1730 /usr/local/bin/alertmanager --config.file=/etc/alertmanager/alertmanager.yml --storage.path=/var/lib/alertmanager/data

Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.513Z caller=main.go:181 level=info msg="Starting Alertmanager" version="(version=0.27.0, branch=HEAD, revision=0aa3c2aad1>
Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.516Z caller=main.go:182 level=info build_context="(go=go1.21.7, platform=linux/amd64, user=root@22cd11f671e9, date=202402>
Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.518Z caller=cluster.go:186 level=info component=cluster msg="setting advertise address explicitly" addr=10.42.0.23 port=9>
Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.527Z caller=cluster.go:683 level=info component=cluster msg="Waiting for gossip to settle..." interval=2s
Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.617Z caller=coordinator.go:113 level=info component=configuration msg="Loading configuration file" file=/etc/alertmanager>
Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.621Z caller=coordinator.go:126 level=info component=configuration msg="Completed loading of configuration file" file=/etc>
Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.626Z caller=tls_config.go:313 level=info msg="Listening on" address=[::]:9093
Aug 12 10:39:51 pallavee alertmanager[1730]: ts=2024-08-12T05:09:51.626Z caller=tls_config.go:316 level=info msg="TLS is disabled." http2=false address=[::]:9093
Aug 12 10:39:53 pallavee alertmanager[1730]: ts=2024-08-12T05:09:53.528Z caller=cluster.go:708 level=info component=cluster msg="gossip not settled" polls=0 before=0 now=1 elapsed=2.0007791>
Aug 12 10:40:01 pallavee alertmanager[1730]: ts=2024-08-12T05:10:01.530Z caller=cluster.go:700 level=info component=cluster msg="gossip settled; proceeding" elapsed=10.002903882s
lines 1-20/20 (END)
```
## Troubleshooting
```
journalctl -u alertmanager.service
```

- To access node exporter go to the web browser and enter
```
localhost: 9093
```
































