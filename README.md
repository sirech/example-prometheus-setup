# Example Prometheus

This is a sample setup of [prometheus](https://prometheus.io/). It can be used to test all the typical functionalities provided by it.

## Running it

[docker-compose](https://docs.docker.com/compose/) is enough to run everything, simply by doing:

```
docker-compose up --build
```

You can access the different components:

- [prometheus](http://localhost:9090)
- [grafana](http://localhost:3000) (user: root / pwd: root)
- [alert-manager](http://localhost:9093)
- [node-exporter](http://localhost:9100)
- [cadvisor](http://localhost:8080)
- [blackbox-exporter](http://localhost:9115)

**NOTE:** This setup is not production ready. There is no network distinction and all services are exposed.

### Restarting prometheus

When changing the configuration, you can restart prometheus through this command

```
curl -X POST http://localhost:9090/-/reload
```

Similarly, for the alert-manager:

```
curl -X POST http://localhost:9090/-/reload
```

## Metrics scraped

There are a bunch of sources configured:

- **prometheus** 

Prometheus scrapes itself

- [**alert-manager**](https://prometheus.io/docs/alerting/latest/alertmanager/)

Scrapes data about generated alerts. Such as `alertmanager_alerts`.

- [**node-exporter**](https://github.com/prometheus/node_exporter)

Scrapes data about the underlying host. Such as `node_cpu_seconds_total`.

- [**cadvisor**](https://github.com/google/cadvisor)

Scrapes data about running containers. Such as `container_cpu_usage_seconds_total`.

- [**black box exporter**](https://github.com/prometheus/blackbox_exporter/)

Checks whether a list of defined target urls is online or not, sort of like a homegrown [freshping](https://www.freshworks.com/website-monitoring/). Note that the actual targets are verified by the `probe_success` metric.

- different jvm based applications ([cookery2](https://github.com/sirech/cookery2-backend), [echo](https://github.com/sirech/echo))

Scrapes metrics provided by the application or the jvm, such as `jvm_threads_states_threads`.

## Sample metrics

The number of samples ingested by prometheus per second:

- [rate(prometheus_tsdb_head_samples_appended_total[5m])](http://localhost:9090/graph?g0.expr=rate(prometheus_tsdb_head_samples_appended_total%5B5m%5D)&g0.tab=1&g0.stacked=0&g0.range_input=1h)

A rate incorporating requests sent by prometheus, and a second one aggregated:

- [rate(prometheus_http_requests_total{code="200", job="prometheus"}[5m])](http://localhost:9090/graph?g0.expr=rate(prometheus_http_requests_total%7Bcode%3D%22200%22%2C%20job%3D%22prometheus%22%7D%5B5m%5D)&g0.tab=1&g0.stacked=0&g0.range_input=15m)
- [sum(rate(prometheus_http_requests_total{code="200", job="prometheus"}[5m]))](http://localhost:9090/graph?g0.expr=sum(rate(prometheus_http_requests_total%7Bcode%3D%22200%22%2C%20job%3D%22prometheus%22%7D%5B5m%5D))&g0.tab=1&g0.stacked=0&g0.range_input=15m)

Aggregated heap consumption for JVM applications

[sum by(instance)(sum_over_time(jvm_memory_used_bytes{area="heap"}[1h]))](http://localhost:9090/graph?g0.expr=sum%20by(instance)(sum_over_time(jvm_memory_used_bytes%7Barea%3D%22heap%22%7D%5B1h%5D))&g0.tab=0&g0.stacked=0&g0.range_input=1h)

Probes set up by the blackbox exporter that are failing

[probe_success == 0](http://localhost:9090/graph?g0.expr=probe_success%20%3D%3D%200&g0.tab=1&g0.stacked=0&g0.range_input=1h)

## Grafana

The grafana instance comes with some dashboards preinstalled as a starting point.

## Alert Manager

The alert manager comes preconfigured with a bunch of alerts. You can simulate a failure that will trigger an alert by doing

```
docker-compose stop echo
```

Critical alerts are sent to a custom [receiver](./receiver). To see the alerts, you can check its logs:

```
docker logs receiver
```
