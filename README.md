# Grafana and Prometheus with cAdvisor on Docker ARM (armv7 / RaspberryPi)

## Forewords
Google does provide ARM Images for RaspberryPi now and since v0.49.1 it has also Podman support in it.

## Configuration
Please check the published ports or docker container names if something was changed.
The network has to be the same for all containers!

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

## (Rootless) Podman
Podman is also supported and should be configured like:

```
podman run -d --name cadvisor \
        --volume=/:/rootfs:ro \
        --volume=/var/run/podman:/var/run/podman:ro \
        --volume=/sys:/sys:ro \
        --volume=/dev/disk:/dev/disk:ro \
        --device=/dev/kmesg \
        --privileged \
        --restart=always \
        --network=host \
        gcr.io/cadvisor/cadvisor-arm:v0.49.1 --podman="unix:///var/run/podman/podman.sock" --housekeeping_interval=10s --docker_only=true
```

The rootless podman configuration differs only in the location of _podman.sock_

```
...
 --volume=/run/user/$(id -u)/podman:/var/run/podman:ro # _podman.sock_ runs under a specific user
```

## Finish and Run
* cAdvisor and Prometheus can be tested by their published ports (cAdvisor: 8080, Prometheus: 9090)
* For Grafana the default password has to be changed (Default: user:admin, password:admin) after first login
* Add Grafana Data Source Prometheus. Prometheus is located under http://prometheusURL:909; Save & test.
* Import a dashboard from Grafana.com, e.g. 11600 (https://grafana.com/grafana/dashboards/11600), 14282 (https://grafana.com/grafana/dashboards/14282-cadvisor-exporter/) or 13496 (https://grafana.com/grafana/dashboards/13496-docker-and-system-monitoring/)

## Documentation
* https://prometheus.io/docs/guides/cadvisor/
* https://medium.com/@mertcan.simsek276/docker-monitoring-with-cadvisor-prometheus-and-grafana-adefe1202bf8
* https://www.learncloudnative.com/blog/2021-08-25-cadvisor
* https://github.com/google/cadvisor/blob/master/docs/storage/prometheus.md
* https://www.metricfire.com/blog/monitoring-docker-containers-with-cadvisor/
* https://grafana.com/docs/grafana/latest/setup-grafana/set-up-https/
* https://github.com/solo5star/dashboard
* https://akashrajpurohit.com/blog/optimizing-cadvisor-for-lower-cpu-usage/

## Pitfalls
* If you encounter something like _failed to get container "/system.slice" with error: unable to find data in memory cache_ then a command argument has to be added in the docker-compose.yml: --raw_cgroup_prefix_whitelist=/docker/
(see https://gitanswer.com/exclude-system-slice-metrics-from-being-exposed-as-prometheus-metrics-cadvisor-go-391749466)
* The network of cAdvisor under Podman is _host_. cAdvisor on an other (internal) or same network as Grafana/Prometheus is not working.
