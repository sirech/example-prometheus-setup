# Example Prometheus

This is a sample setup of [prometheus](https://prometheus.io/). It can be used to test all the typical functionalities provided by it.

## Running it

[docker-compose](https://docs.docker.com/compose/) is enough to run everything, simply by doing:

```
docker-compose up
```

## Metrics scraped

There are a bunch of typical metrics being scraped, including

- **prometheus** itself


## Prometheus UI

The UI of prometheus can be accessed through http://localhost:9090.

A rate incorporating requests sent by prometheus, and a second one aggregated:

- [rate(prometheus_http_requests_total{code="200", job="prometheus"}[5m])](http://localhost:9090/graph?g0.expr=rate(prometheus_http_requests_total%7Bcode%3D%22200%22%2C%20job%3D%22prometheus%22%7D%5B5m%5D)&g0.tab=1&g0.stacked=0&g0.range_input=15m)
- [sum(rate(prometheus_http_requests_total{code="200", job="prometheus"}[5m]))](http://localhost:9090/graph?g0.expr=sum(rate(prometheus_http_requests_total%7Bcode%3D%22200%22%2C%20job%3D%22prometheus%22%7D%5B5m%5D))&g0.tab=1&g0.stacked=0&g0.range_input=15m)
