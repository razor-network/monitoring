# Monitoring

This repo contains the monitoring setup for [razor-go](https://github.com/razor-network/razor-go) staker along with the host in which it is running.


We are using
1.  [Victoria Metrics](https://victoriametrics.com/): To scrap prometheus metrics form exporters.
2. [Grafana](https://grafana.com/): For dashboard on metrics
3. [loki](https://grafana.com/oss/loki/): Aggregate logs and provide to grafana.
4. [promtail](https://grafana.com/docs/loki/latest/clients/promtail/):An agent which ships the contents of local logs
5. [node-exporter](https://prometheus.io/docs/guides/node-exporter/): node-exporter provide host based metrics to Victoria scraper
6. [caAdvisor](https://prometheus.io/docs/guides/cadvisor/): caAdvisor provide containers based metrics to Victoria scraper
7. [Alertmanager](https://github.com/prometheus/alertmanager.git): Via Alertmanager we can configure alert for our staker and host.


### Prerequisites 

- You must have Docker and Docker Compose installed.
- If you are running staker via docker and running on same host, then your staker should up and running with `razor_network` can refer [here](https://github.com/razor-network/razor-go/tree/v1-audit#docker-quick-start)
- You must expose razor-go metrics, can refer [here](https://github.com/razor-network/razor-go/tree/v1-audit#expose-metrics) 



>**_NOTE:_**  `razor_network` is a docker based bridge network through which staker and monitoring stack communicate securely without exposing anything publicly.



### Configuration 

- If your staker is running via binary, then 
    
    1. In `./configs/prometheus.yml`, replace `"razor-go:2112"` with `"<private/public address of host>:2112"`

- For alerting you can add webhook in `./configs/alertmanager.yml`, replace `http://127.0.0.1:5001/` with your webhook URL. This will send you an alert in every 5min if metrics stops.


### Start monitoring stack
-  You can spin all agents at once via 
        
    ```
    docker-compose up -d
    ``` 
    Can check the status of each service via
    ```
    docker-compose ps
    ```

- You can open grafana at `<private/public address of host>:3000`, and get 
    1. Can checkout `Razor` dashboard to monitor your staker.
    2. Insight of host metrics at `Node Exporter Full` dashboard.
    3. Containers Insight at `Docker and OS metrics ( cadvisor, node_exporter )` dashboard.
    4. Can monitor alerts at `Alertmanager` dashboard.
    
>**_NOTE:_** Configure firewall for port `3000` on your host to access grafana.

### Troubleshoot Alerting

1. In `docker-compose.yml` uncomment ports for `alertmanager` and `vmalert`.
2. Configure firewall to allow access to ports `8880` and `9093`.

3. Check you get alerts on vmalert via `http://<host_address>:8880/vmalert/alerts`. vmalert is configured to scrap in every 2min. 

4. If you see alert in vmalert then look into alertmanager `http://<host_address>:9093/#/alerts?`, if you see alerts in there but you didn't get one then probably you need to check your weebhook.