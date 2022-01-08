# Grafana and Prometheus on ARM with Docker

## Forewords
Unfortunaltely Google doesn't provide any cAdvisor version for ARM. The docker image from zcube/cadvisor is used for ARM (https://github.com/zcube/cadvisor-docker and https://hub.docker.com/r/zcube/cadvisor).

## Configuration
Please check the published ports or docker container names if sth. was changed

## Installation
Run the docker-compose.yml.
All containers show up and a new network was build (e.g. _prometheus_default_)

## Finish and Run
* cAdvisor and Prometheus can be tested by their published ports (cAdvisor: 8080, Prometheus: 9090)
* For Grafana the default password has to be changed (user:admin, password:admin) after first login
* Add Grafana Data Source Prometheus. Prometheus is located under http://prometheus:9090, save & test.
* Import a dashboard from Grafana.com, e.g. 11600 (https://grafana.com/grafana/dashboards/11600)

## Documentation
* https://prometheus.io/docs/guides/cadvisor/
* https://medium.com/@mertcan.simsek276/docker-monitoring-with-cadvisor-prometheus-and-grafana-adefe1202bf8
* https://www.learncloudnative.com/blog/2021-08-25-cadvisor
* https://github.com/google/cadvisor/blob/master/docs/storage/prometheus.md
* https://www.metricfire.com/blog/monitoring-docker-containers-with-cadvisor/

## Pitfalls
If you encounter something like _failed to get container "/system.slice" with error: unable to find data in memory cache_ then a command argument has to be added in the docker-compose.yml: --raw_cgroup_prefix_whitelist=/docker/
(see https://gitanswer.com/exclude-system-slice-metrics-from-being-exposed-as-prometheus-metrics-cadvisor-go-391749466)

## Open thingies
* Is Redis really needed??
* Make Grafana Configuration persistent (will be resetted if Grafana Container is restarted)
* Security ...
