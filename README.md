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

## Redmine set up in podman 
- Install Podman
```
sudo apt update 
sudo apt install podman
```
- Create a Pod for Redmine
```
podman pod create --name redmine -p 3100:3000
```
# Create a folder for PostgreSQL data
```
echo "Creating folder for PostgreSQL data..."
mkdir -p /home/pallavee/data/redmine/postgres
```
# Change ownership of the folder
```
echo "Changing ownership of the folder..."
podman unshare chown 999:999 /home/pallavee/data/redmine/postgres
```
# Create the Redmine Pod
```
echo "Creating the Redmine Pod..."
podman pod create --name redmine --publish 3100:3000 --publish 5433:5432
```
# Run the PostgreSQL container
```
echo "Running the PostgreSQL container..."
podman run -dt \
    --pod redmine \
    --name redmine-postgres \
    -e POSTGRES_DB=keen \
    -e POSTGRES_USER=postgres \
    -e POSTGRES_PASSWORD=password \
    -e POSTGRES_HOST_AUTH_METHOD=trust \
    -e PGDATA=/var/lib/postgresql/data/pgdata \
    -v /home/pallavee/data/redmine/postgres:/var/lib/postgresql/data \
  docker.io/postgres:latest
```
# Run the Redmine application container
```
echo "Running the Redmine application container..."
podman run -dt \
    --pod redmine \
    --name redmine-app \
    -e REDMINE_DB_POSTGRES=127.0.0.1 \
    -e REDMINE_DB_PORT=5432 \
    -e REDMINE_DB_DATABASE=keen \
    -e REDMINE_DB_USERNAME=postgres \
    -e REDMINE_DB_PASSWORD=password \
    docker.io/redmine:latest
```
## Access Redmine
- Once the containers are running, you can access Redmine by navigating to
```
http://localhost:8080 in your web browser.
```

## Start redemine pod usind 
```
Podman pod start redmine
```
## Start redmine using 
```
Podman start redmine-app
```
## Check status of Redmine
```
podman ps
```
## Output
```
CONTAINER ID  IMAGE                              COMMAND               CREATED     STATUS             PORTS                                           NAMES
852698b6d58f  k8s.gcr.io/pause:3.5                                     5 days ago  Up 58 minutes ago  0.0.0.0:3100->3000/tcp, 0.0.0.0:5433->5432/tcp  a052f97c8609-infra
55b159943b4f  docker.io/library/postgres:latest  postgres              5 days ago  Up 58 minutes ago  0.0.0.0:3100->3000/tcp, 0.0.0.0:5433->5432/tcp  redmine-postgres
ddf1f6011867  docker.io/library/redmine:latest   rails server -b 0...  5 days ago  Up 58 minutes ago  0.0.0.0:3100->3000/tcp, 0.0.0.0:5433->5432/tcp  redmine-app
```
## To Cheack database in redmine
```
podman exec -it redmine-app bash
```
```
cd /usr/src/redmine/config
```
## To see database info use
```
cat database.yml
```
## Output
```
production:
  adapter: "postgresql"
  host: "127.0.0.1"
  port: "5432"
  username: "postgres"
  password: "password"
  database: "keen"
  encoding: "utf8"
```
```
podman exec -it redmine-postgres bash
```
## Allow Email Notification in Redmine
## Configure Email Notifications:
- login redmine
- Go to 'Administration' >'setting'>'email notification'
- Check "Send email notifications".
- Check "Issue added", "Issue updated", and "Issue assigned"
## Set Up Mail Server Settings:
- GO inside container
```
podman exec -it redmine-app /bin/bash
cp configuration.example.yml configuration.yml
cd /usr/src/redmine/config
nano configuration.yml
```
- Add following content in Producation block
```
production:
  email_delivery:
    delivery_method: :smtp
    smtp_settings:
      address: "smtp.gmail.com"
      port: 587
      domain: "example.com"
      authentication: :login
      user_name: "your_email@example.com"
      password: "your_email_password"
      enable_starttls_auto: true
```
- restart redmine application.
```
podman restart redmine-app
```
## Integrating node exporter and alertmanager with prometheus
- Follow this steps for integrating prometheus node exporter and alertmanager
- Go to
```
cd /etc/prometheus/prometheus.yml
Using nano prometheus.yml
```
- Add folowing content in file
```
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
           - localhost:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
 #  - "rules1.yml" 
   - "rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
# grafana 
  - job_name: "grafana"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:3000"]

#node-exporter
  - job_name: "node_exporter"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9100"]

```
- After adding press ctrl+x
- Ctrl+y
- Enter

## Add rule file for triggering alert 
```
Cd /etc/prometheus
Ls
Nano rules.yml
```
## Add follow content 
```

groups:
  - name: alerts
    rules:
      # Alert for when Node Exporter is down
      - alert: NodeExporterDown
        expr: up{job="node_exporter"} == 0
        for: 1m
        labels:
          severity: critical
          priority: priority1
        annotations:
          summary: "NodeExporterDown"
          description: "Node Exporter has been down for more than 1 minute."
          status: "In Progress"
          assignee: "test test"
          start_date: "2024-07-22"
          due_date: "2024-07-30"
          done_ratio: "50"

      - alert: HighCPUUsageInfo
        expr: 100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 50
        for: 1m
        labels:
          severity: info
          priority: priority8
        annotations:
          summary: "HighCPUUsageInfo"
          description: "CPU usage has been above 50% for more than 1 minute."
          status: "In Progress"
          assignee: "test test"
          start_date: "2024-07-22"
          due_date: "2024-07-30"
          done_ratio: "50"

      # Alert for when CPU usage exceeds 70% (Warning)
      - alert: HighCPUUsageWarning
        expr: 100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 70
        for: 1m
        labels:
          severity: warning
          priority: priority2
        annotations:
          summary: "HighCPUUsageWarning"
          description: "CPU usage has been above 70% for more than 1 minute."
          status: "In Progress"
          assignee: "test test"
          start_date: "2024-07-22"
          due_date: "2024-07-30"
          done_ratio: "50"

      # Alert for when CPU usage exceeds 90% (Critical)
      - alert: HighCPUUsageCritical
        expr: 100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 90
        for: 1m
        labels:
          severity: critical
          priority: priority1
        annotations:
          summary: "HighCPUUsageCritical"
          description: "CPU usage has been above 90% for more than 1 minute."
          status: "In Progress"
          assignee: "test test"
          start_date: "2024-07-22"
          due_date: "2024-07-30"
          done_ratio: "50"

      # Alert for when the Apache service is down
      - alert: apacheDown
        expr: up{job="apache_exporter"} == 0
        for: 1m
        labels:
          severity: warning
          priority: priority14
        annotations:
          summary: "apacheDown"
          description: "Apache service is down."
          status: "In Progress"
          assignee: "test test"
          start_date: "2024-07-22"
          due_date: "2024-07-30"
          done_ratio: "50"


       
```
## Integrating webhook in alertmanager
```
Cd /etc/alertmanager
Ls
Nano alertmanager.yml
```
### Add following content
```
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'alertmanager52@gmail.com'
  smtp_auth_username: 'alertmanager52@gmail.com'
  smtp_auth_password: 'kotlsd'
  smtp_auth_identity: 'alertmanager52@gmail.com'

route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 3h
  receiver: 'email-receiver'
  
  routes:
  - receiver: 'webhook-receiver'
    matchers:
    - alertname=~".*"

receivers:
- name: 'email-receiver'
  email_configs:
  - to: 'testpallaveetest@gmail.com'
    send_resolved: true
    headers:
      Subject: '[{{ .CommonLabels.alertname }}]'
    html: |
      <!DOCTYPE html>
      <html>
      <head>
      </head>
      <body>
          <div class="content">
              Tracker: Monitioring-tracker<br>
              Status: {{ range .Alerts }}{{ .Annotations.status }}{{ end }}<br>
              Priority: {{ range .Alerts }}{{ .Labels.priority }}{{ end }}<br>
              Assignee: {{ range .Alerts }}{{ .Annotations.assignee }}{{ end }}<br>
              Start date: {{ range .Alerts }}{{ .Annotations.start_date }}{{ end }}<br>
              Due date: {{ range .Alerts }}{{ .Annotations.due_date }}{{ end }}<br>
              Done ratio: {{ range .Alerts }}{{ .Annotations.done_ratio }}{{ end }}<br>
              Description: {{ range .Alerts }}{{ .Annotations.description }}{{ end }}<br>
          </div>
      </body>
      </html>

- name: 'webhook-receiver'
  webhook_configs:
  - url: 'http://192.168.0.107:5100/api/v1/alerts'
    send_resolved: true
```
## Set up for webhook

## Install python and Quart
```
pip install Quart requests
```
## Check python Installed sucessfull
```
python --version
```
- create file for webhook
```
nano alert_webhook.py
```
## Add following content in file to create ticket in redmine
```
from quart import Quart, request, jsonify
import httpx
from datetime import datetime, timedelta
import itertools
import asyncio
import aiofiles

app = Quart(__name__)

# Redmine API Configuration
REDMINE_API_URL = 'http://localhost:3100/issues.json'
REDMINE_API_KEY = '2e38203bd529ca0fd3cbfdd970ae70835182c538'

# File to store mapping of alert names to Redmine issue IDs
ISSUE_MAP_FILE = 'issues_map.txt'

# List of members' IDs (Redmine User IDs) in the project
members = itertools.cycle([9, 10, 11])  # Replace with actual Redmine User IDs

# Priority mapping based on severity
PRIORITY_MAPPING = {
    'critical': 1,  # Priority 1 in Redmine
    'warning': 2,   # Priority 2 in Redmine
    'info': 3       # Priority 3 in Redmine
}

# Estimated time mapping based on severity (in hours)
ESTIMATE_TIME_MAPPING = {
    'critical': 1,  # Estimate 1 hour
    'warning': 2,   # Estimate 2 hours
    'info': 3       # Estimate 3 hours
}

async def save_issue_id(alertname, issue_id):
    """Save the issue ID associated with an alert name."""
    print(f"[INFO] Starting to save issue ID {issue_id} for alert {alertname}")
    async with aiofiles.open(ISSUE_MAP_FILE, 'a') as f:
        await f.write(f"{alertname}:{issue_id}\n")
    print(f"[INFO] Finished saving issue ID {issue_id} for alert {alertname}")

def get_next_assignee():
    """Get the next member in the cycle to assign the issue."""
    assignee_id = next(members)
    print(f"[INFO] Next assignee ID is {assignee_id}")
    return assignee_id

async def create_redmine_issue(alert, retry_attempts=3):
    """Creates a Redmine issue and retries in case of failure."""
    print(f"[INFO] Starting to create a Redmine issue for alert {alert['labels'].get('alertname')}")
    
    headers = {
        'X-Redmine-API-Key': REDMINE_API_KEY,
        'Content-Type': 'application/json'
    }
    
    # Calculate start date and due date
    start_date = datetime.now().date().isoformat()  # Current date in YYYY-MM-DD format
    due_date = (datetime.now().date() + timedelta(days=1)).isoformat()  # Due date is start date + 1 day
    print(f"[INFO] Start date is {start_date} and due date is {due_date}")
    
    assignee_id = get_next_assignee()  # Get the next member to assign the issue
    
    # Determine priority and estimate time based on severity
    severity = alert['labels'].get('severity', 'info').lower()
    priority_id = PRIORITY_MAPPING.get(severity, 3)  # Default to Priority 3 if severity not found
    estimated_hours = ESTIMATE_TIME_MAPPING.get(severity, 3)  # Default to 3 hours if severity not found
    print(f"[INFO] Severity is {severity}, setting priority to {priority_id} and estimate time to {estimated_hours} hours")
    
    issue_data = {
        "issue": {
            "project_id": 2,  # Project ID for 'monitoring'
            "subject": alert.get('annotations', {}).get('summary', 'No summary provided'),
            "description": alert.get('annotations', {}).get('description', 'No description provided'),
            "priority_id": priority_id,  # Priority based on severity
            "status_id": 1,  # Set the issue status to "New" when created
            "assigned_to_id": assignee_id,  # Assign the issue to the next member
            "start_date": start_date,  # Start date
            "due_date": due_date,  # Due date
            "estimated_hours": estimated_hours  # Estimate time based on severity
        }
    }
    
    for attempt in range(retry_attempts):
        print(f"[INFO] Attempt {attempt + 1} to create Redmine issue for alert {alert['labels'].get('alertname')}")
        async with httpx.AsyncClient() as client:
            try:
                response = await client.post(REDMINE_API_URL, json=issue_data, headers=headers)
                print(f"[INFO] Redmine response status: {response.status_code}")
                print(f"[INFO] Redmine response content: {response.text}")
                
                if response.status_code == 201:
                    issue_id = response.json().get('issue', {}).get('id')
                    print(f"[INFO] Successfully created Redmine issue with ID {issue_id} for alert {alert['labels'].get('alertname')}")
                    await save_issue_id(alert['labels'].get('alertname'), issue_id)
                    return issue_id
                else:
                    print(f"[WARNING] Failed to create Redmine issue for alert {alert['labels'].get('alertname')} (attempt {attempt + 1})")
            except Exception as e:
                print(f"[ERROR] Error creating Redmine issue (attempt {attempt + 1}): {e}")
        
        print(f"[INFO] Waiting 2 seconds before retrying...")
        await asyncio.sleep(2)  # Delay between retry attempts
    print(f"[ERROR] All attempts to create Redmine issue for alert {alert['labels'].get('alertname')} failed")
    return None

async def handle_alert(alert):
    """Process individual alerts asynchronously."""
    alertname = alert['labels'].get('alertname')
    print(f"[INFO] Processing alert: {alertname}")
    if alert['status'] == 'firing':
        print(f"[INFO] Alert {alertname} is firing, creating a Redmine issue")
        issue_id = await create_redmine_issue(alert)
        if issue_id:
            print(f"[INFO] Created Redmine issue with ID {issue_id}")
        else:
            print(f"[ERROR] Failed to create Redmine issue for alert {alertname}")

@app.route('/api/v1/alerts', methods=['GET', 'POST'])
async def receive_alert():
    if request.method == 'POST':
        print("[INFO] Starting to process received alert")
        alert_data = await request.get_json()
        print(f"[INFO] Received alert data: {alert_data}")

        tasks = []
        for alert in alert_data.get('alerts', []):
            print(f"[INFO] Queuing task for alert {alert['labels'].get('alertname')}")
            tasks.append(handle_alert(alert))
        
        print("[INFO] Processing all queued alerts concurrently")
        await asyncio.gather(*tasks)  # Process all alerts concurrently

        print("[INFO] Finished processing received alert")
        return jsonify({'status': 'received'}), 200
    else:
        print("[INFO] Webhook endpoint is running.")
        return "Webhook endpoint is running.", 200

if __name__ == '__main__':
    print("[INFO] Starting webhook server")
    uvicorn.run(app, host='0.0.0.0', port=5100, debug=True)
    print("[INFO] Webhook server is running")



```

```
check for closing ticket

import httpx
import asyncio

# Redmine API Configuration
REDMINE_ISSUES_URL = 'http://localhost:3100/issues'
REDMINE_API_KEY = '2e38203bd529ca0fd3cbfdd970ae70835182c538'

async def get_active_alerts():
    """Fetch active alerts from Alertmanager."""
    print("Fetching active alerts from Alertmanager...")
    async with httpx.AsyncClient() as client:
        response = await client.get('http://localhost:9093/api/v2/alerts')
        print(f"Alertmanager response status: {response.status_code}")
        if response.status_code == 200:
            alerts = response.json()
            active_alerts = {alert['labels']['alertname'].strip() for alert in alerts}
            print(f"Active alerts: {active_alerts}")
            return active_alerts
        print("Failed to fetch active alerts from Alertmanager.")
        return set()

async def get_resolved_issues():
    """Fetch resolved issues from Redmine."""
    print("Fetching resolved issues from Redmine...")
    headers = {
        'X-Redmine-API-Key': REDMINE_API_KEY,
        'Content-Type': 'application/json'
    }
    params = {
        'status_id': 3  # Status ID for 'Resolved'
    }
    async with httpx.AsyncClient() as client:
        response = await client.get(f'{REDMINE_ISSUES_URL}.json', headers=headers, params=params)
        print(f"Redmine response status: {response.status_code}")
        if response.status_code == 200:
            issues = response.json().get('issues', [])
            resolved_issues = {issue['id']: issue['subject'].split()[0].strip() for issue in issues}
            print(f"Resolved issues: {resolved_issues}")
            return resolved_issues
        print("Failed to fetch resolved issues from Redmine.")
        return {}

async def close_or_reassign_tickets():
    """Check if resolved issues in Redmine should be reassigned or closed."""
    print("Checking if resolved issues in Redmine should be reassigned or closed...")
    active_alerts = await get_active_alerts()
    resolved_issues = await get_resolved_issues()
    
    for issue_id, alertname in resolved_issues.items():
        if alertname in active_alerts:
            # Reassign issue if it's still in Alertmanager
            print(f"Alert {alertname} is still active, reassigning Redmine issue {issue_id}.")
            await reopen_redmine_issue(issue_id, alertname)
        else:
            # Close issue if it's not in Alertmanager anymore
            print(f"Alert {alertname} is no longer active, closing Redmine issue {issue_id}.")
            await close_redmine_issue(issue_id)

async def reopen_redmine_issue(issue_id, alertname):
    print(f"Reassigning Redmine issue {issue_id} due to unresolved alert {alertname}.")
    url = f'{REDMINE_ISSUES_URL}/{issue_id}.json'
    headers = {
        'X-Redmine-API-Key': REDMINE_API_KEY,
        'Content-Type': 'application/json'
    }
    issue_data = {
        "issue": {
            "status_id": 6,  # Set to 'Reassigned'
            "notes": "Issue not resolved yet, please fix it."
        }
    }
    async with httpx.AsyncClient() as client:
        response = await client.put(url, json=issue_data, headers=headers)
        print(f"Redmine response for reassigning issue {issue_id}: {response.status_code}")

async def close_redmine_issue(issue_id):
    print(f"Closing Redmine issue {issue_id}.")
    url = f'{REDMINE_ISSUES_URL}/{issue_id}.json'
    headers = {
        'X-Redmine-API-Key': REDMINE_API_KEY,
        'Content-Type': 'application/json'
    }
    issue_data = {
        "issue": {
            "status_id": 4  # Set to 'Closed'
        }
    }
    async with httpx.AsyncClient() as client:
        response = await client.put(url, json=issue_data, headers=headers)
        print(f"Redmine response for closing issue {issue_id}: {response.status_code}")

async def resolve_new_issues():
    """Automatically resolve 'New' Redmine issues that have no active alerts."""
    print("Resolving 'New' Redmine issues that have no active alerts...")
    headers = {
        'X-Redmine-API-Key': REDMINE_API_KEY,
        'Content-Type': 'application/json'
    }
    params = {
        'status_id': 1  # Status ID for 'New'
    }
    async with httpx.AsyncClient() as client:
        response = await client.get(f'{REDMINE_ISSUES_URL}.json', headers=headers, params=params)
        print(f"Redmine response for 'New' issues: {response.status_code}")
        if response.status_code == 200:
            issues = response.json().get('issues', [])
            active_alerts = await get_active_alerts()
            for issue in issues:
                alertname = issue['subject'].split()[0].strip()
                if alertname not in active_alerts:
                    issue_id = issue['id']
                    print(f"Alert {alertname} is no longer active, resolving Redmine issue {issue_id} automatically.")
                    await close_redmine_issue_as_resolved_automatic(issue_id)

async def close_redmine_issue_as_resolved_automatic(issue_id):
    print(f"Automatically resolving Redmine issue {issue_id}.")
    url = f'{REDMINE_ISSUES_URL}/{issue_id}.json'
    headers = {
        'X-Redmine-API-Key': REDMINE_API_KEY,
        'Content-Type': 'application/json'
    }
    issue_data = {
        "issue": {
            "status_id": 5  # Set to 'Resolved-Automatic'
        }
    }
    async with httpx.AsyncClient() as client:
        response = await client.put(url, json=issue_data, headers=headers)
        print(f"Redmine response for automatically resolving issue {issue_id}: {response.status_code}")

async def main():
    """Main function to handle ticket updates."""
    print("Starting ticket management loop...")
    try:
        while True:
            print("Checking tickets for reassignment or closure...")
            await close_or_reassign_tickets()
            print("Checking for new issues to resolve automatically...")
            await resolve_new_issues()
            print("Sleeping for 60 seconds...")
            await asyncio.sleep(60)  # Run every 60 seconds
    except KeyboardInterrupt:
        print("Script interrupted, shutting down gracefully.")

if __name__ == '__main__':
    print("Starting ticket management script.")
    
    try:
        asyncio.run(main())
    except KeyboardInterrupt:
        print("Main script interrupted by user (Ctrl + C), exiting.")


```
```
 ## to close ticket use python3 manage_ticket.py 
```

## Command to run webhook
```
uvicorn updated_alert_webhook:app --host 0.0.0.0 --port 5100 --reload
```

## redmine configuration file
```
roduction:
  email_delivery:
    delivery_method: :smtp
    smtp_settings:
      address: "smtp.gmail.com"              # Gmail SMTP server address
      port: 587                              # Gmail SMTP port (TLS)
      domain: "gmail.com"                    # Your domain
      authentication: :login                 # Authentication method
      user_name: "pallaveechaubey831@gmail.com"       # Your Gmail address
      password: "buhqfvumzpjimldm"               # Your Gmail password or app-specific passwo>
      enable_starttls_auto: true              # Use TLS

```


















































