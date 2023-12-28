# InfluxDB + Grafana ProxMox
This guide will walk through the steps to configure Metric Server of ProxMox
## Prerequisites
Before you begin, you should have the following:
- Debian 12 system with **root** access. ( could be a countainer )

## Installation Steps
1. Update the package list
```
apt update
```

2. Upgrade the system:
```
apt upgrade
```

3. Install influxDB
```
apt install -y influxdb influxdb-client
```

4. Configure influxDB
```
nano /etc/influxdb/influxdb.conf
```
```
'
...
[[udp]]
	enabled = true
	bind-address = "0.0.0.0:8089"
	database = "proxmox"
	batch-size = 1000
	batch-timeout = "1s"
	# retention-policy = ""
'
```

5.  Configure Metric Server on Proxmox
Datacenter > Metric Server > Add > InfluxDB
Name= proxmox
Server= "InfluxDB server IP"
Port= 8089
Protocol= UDP

6.  Restart and enable service influxdb
```
systemctl restart influxdb
```
```
systemctl enable influxdb
```
```
systemctl status influxdb
```
7. Check influxDB receaving data form Proxmox
```
influx
> show databases
....
__internal
proxmox
```
8. Install Grafana
```
sudo apt-get install -y adduser libfontconfig1 musl  
wget https://dl.grafana.com/oss/release/grafana_10.2.3_amd64.deb
sudo dpkg -i grafana_10.2.3_amd64.deb
```

9. Restart and enable service grafana-server
```
systemctl restart grafana-server
```
```
systemctl enable grafana-server
```
```
systemctl status grafana-server
```
 
10. Access grafana-server
https://"SERVER_IP":3000
user: admin
password: admin

11. Add datasource influxdb grafana-server
name: proxmox
Query Language: InfluxQL
URL: http://localhost:8086
Database: proxmox

12. Add dashboard
https://github.com/flaviojunior1995/proxmox/blob/main/influxdb/grafana-dashboard/proxmox.json

14. Choose datasource for dashboard
