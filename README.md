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
- Creating systemd service file
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
- Start the Prometheus Service
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
