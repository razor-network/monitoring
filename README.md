aggregation# Monitoring

This repo contains the monitoring setup for [razor-go](https://github.com/razor-network/razor-go) staker along with the host in which it is running.

We are using
1.  [Victoria Metrics](https://victoriametrics.com/): To scrap prometheus metrics form exporters.
2. [Grafana](https://grafana.com/): For dashboard on metrics
3. [loki](https://grafana.com/oss/loki/): Aggregate logs and provide to grafana.
4. [promtail](https://grafana.com/docs/loki/latest/clients/promtail/):An agent which ships the contents of local logs
5. [node-exporter](https://prometheus.io/docs/guides/node-exporter/): node-exporter provide host based metrics to Victoria scraper
6. [caAdvisor](https://prometheus.io/docs/guides/cadvisor/): caAdvisor provide containers based metrics to Victoria scraper


Before running the monitoring stack, you should have staker up and running with `razor_network`, razor_network is a docker based bridge network through which staker and monitoring stack communicate securely without exposing anything publicly.


You can spin all agents at once via 
 
```
docker-compose up -d
``` 
Can check the status of each container via
```
docker-compose ps
```

>### Note:
> If you see grafana keep restarting, then we need to update `user: 472` in `docker-compose.yml` for grafana service, if you are planing to run that as root then can add `user: 0` or if not, you can get your user_id via `id` command and update `user: <your_user_id>`, after this we need to run `docker-compose restart grafana`


 You can open grafana at `localhost:3000`, and get 
 1. Insight of host metrics at `Node Exporter Full` dashboard
 2. Containers Insight at `Docker and OS metrics ( cadvisor, node_exporter )` dashboard
 3. Can checkout `Razor` dashboard to monitor your staker
