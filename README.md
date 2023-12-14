# Grafana and Prometheus with cAdvisor on Docker ARM (armv7 / RaspberryPi)

## Forewords
Unfortunaltely Google doesn't provide any cAdvisor version for ARM. 

The docker image from zcube/cadvisor is used for ARM (https://github.com/zcube/cadvisor-docker and https://hub.docker.com/r/zcube/cadvisor).
__UPDATE:__ Google offers also some arm images. Has to be tested with RaspberryPI.

Podman with cAdvisor is not working, there are different open issues. Full support is still not implemented.

## Configuration
Please check the published ports or docker container names if something was changed

### Prometheus
Prometheus scrapping file should be defined beforehand:
```
-v /path/to/file/prometheus.yml:/etc/prometheus/prometheus.yml:ro
```

For making prometheus data persistent, add:
```
-v /path/to/prometheus:/prometheus:Z
```
  
### Grafana
For making grafana data persistent, add:
```
-v /path/to/grafana:/var/lib/grafana:Z
```

## Installation
Run _docker stack deploy --compose-file docker-compose.yml prometheus_ or _podman-compose -f docker-compose.yml_ (podman-compose has to be installed beforehand https://github.com/containers/podman-compose or https://linuxhandbook.com/podman-compose/)

All containers show up and a new (dedicated) network was build (e.g. _prometheus_default_). Make sure that Docker has some IP Ranges left, otherwise there will be an error concerning overlapping networks.

## Finish and Run
* cAdvisor and Prometheus can be tested by their published ports (cAdvisor: 8080, Prometheus: 9090)
* For Grafana the default password has to be changed (user:admin, password:admin) after first login
* Add Grafana Data Source Prometheus. Prometheus is located under http://prometheusURL:9090, save & test.
* Import a dashboard from Grafana.com, e.g. 11600 (https://grafana.com/grafana/dashboards/11600)

## Documentation
* https://prometheus.io/docs/guides/cadvisor/
* https://medium.com/@mertcan.simsek276/docker-monitoring-with-cadvisor-prometheus-and-grafana-adefe1202bf8
* https://www.learncloudnative.com/blog/2021-08-25-cadvisor
* https://github.com/google/cadvisor/blob/master/docs/storage/prometheus.md
* https://www.metricfire.com/blog/monitoring-docker-containers-with-cadvisor/
* https://grafana.com/docs/grafana/latest/setup-grafana/set-up-https/

## Pitfalls
If you encounter something like _failed to get container "/system.slice" with error: unable to find data in memory cache_ then a command argument has to be added in the docker-compose.yml: --raw_cgroup_prefix_whitelist=/docker/
(see https://gitanswer.com/exclude-system-slice-metrics-from-being-exposed-as-prometheus-metrics-cadvisor-go-391749466)

## Open thingies
* Remove published ports of Prometheus and cAdvisor. Access to these containers is only possible from inside the dedicated network or from a pod
