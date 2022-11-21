# Getting Prometheus Metrics

This guide goes through the necessary steps to get custom metrics in the prometheus format for the QoTD demo application.

Outputs of getting these metrics are: json file, csv file, excel file.

## On the Agent (K8s)

Add this to the configuration.yaml of the agent deployed on an K8s cluster
QotD application writes its metrics on **/metrics**

```
com.instana.plugin.prometheus:
  # Global (all) endpoints username/password configuration (optional)
  username: ''
  password: ''
  customMetricSources:
  - url: '/metrics'            # metrics endpoint, the IP and port are auto-discovered
    metricNameIncludeRegex: '.*'             # regular expression to filter metrics
```

## Get API Token

https://www.ibm.com/docs/en/instana-observability/current?topic=apis-web-rest-api#unit-specific-api-tokens


## Get plugin

Get Prometheus metrics plugin, needed to gather the metrics

```
curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/catalog/plugins-with-custom-metrics' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool

Result:

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    96  100    96    0     0    590      0 --:--:-- --:--:-- --:--:--   592
[
    {
        "plugin": "jvmRuntimePlatform",
        "label": "JVM"
    },
    {
        "plugin": "prometheus",
        "label": "Prometheus App"
    }
]
```

## Get keywords for dynamic focus queries


Get names of the entities used in the queries for specific metrics


```
curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/catalog/search' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool | grep entity.prometheus 

Result:

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 63785    0 63785    0     0   193k      0 --:--:-- --:--:-- --:--:--  192k
        "keyword": "entity.prometheus.summary",
        "keyword": "entity.prometheus.gauge",
        "keyword": "entity.prometheus.histogram",
        "keyword": "entity.prometheus.counter",
        "keyword": "entity.prometheus.untyped",

```


## Get available metrics

For specific entities get the exact name of the metric:

* For **nodejs\_heap\_size\_used\_bytes**

```
curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/catalog/metrics/prometheus' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool | grep nodejs_heap_size_used_bytes

Result:

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 3665k    0 3665k    0     0  1649k      0 --:--:--  0:00:02 --:--:-- 1650k
        "label": "Prometheus gauge nodejs_heap_size_used_bytes",
        "metricId": "metrics.gauges.nodejs_heap_size_used_bytes",
```

* For **process\_cpu\_seconds\_total**

```
 curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/catalog/metrics/prometheus' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool | grep     process_cpu_seconds_total
  
Result
  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 3639k    0 3639k    0     0  1556k      0 --:--:--  0:00:02 --:--:-- 1557k
        "label": "Prometheus counter process_cpu_seconds_total",
        "metricId": "metrics.counters.process_cpu_seconds_total",
```

## Get Snapshot

is the specific resource that contains the metrics you will gather, in a specific moment

### All Prometheus Snapshots

```
 curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/snapshots?plugin=prometheus&offline=true' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool > snapshots_prometheus.json
```

### Snapshot for Quote Service
```
curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/snapshots?plugin=prometheus&offline=true' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool | grep -A1 -B5 172.30.179.4:

Result:

 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  9300    0  9300    0     0  31886      0 --:--:-- --:--:-- --:--:-- 31958
            "snapshotId": "mywvXtaFTesETDLY8wLFek9YCOo",
            "plugin": "prometheus",
            "from": 1668192385000,
            "to": null,
            "tags": [],
            "label": "http://172.30.179.4:3001/metrics",
            "host": "06:ab:62:ff:fe:50:33:94"
	
```
### Snapshot for Rating Service

```
 curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/snapshots?plugin=prometheus&offline=true' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool | grep -A1 -B5 172.30.179.9:
 
Result: 
 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  9300    0  9300    0     0  42123      0 --:--:-- --:--:-- --:--:-- 42081
            "snapshotId": "759HHggWdDEg8jHQJbaIDawabJo",
            "plugin": "prometheus",
            "from": 1668192385000,
            "to": null,
            "tags": [],
            "label": "http://172.30.179.9:3004/metrics",
            "host": "06:ab:62:ff:fe:50:33:94"
```
### Snapshot for Web Service

```
 curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/snapshots?plugin=prometheus&offline=true' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool | grep -A1 -B5 172.30.179.136:
 
Result:

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  9300    0  9300    0     0  26096      0 --:--:-- --:--:-- --:--:-- 26050
            "snapshotId": "L1VthhP9ivxMvvHIp-G7vPyl8v0",
            "plugin": "prometheus",
            "from": 1668220128000,
            "to": null,
            "tags": [],
            "label": "http://172.30.179.136:3000/metrics",
            "host": "06:06:6e:ff:fe:d3:b8:67"
```

## Get details on Specific Snapshot (Optional)

Get more info on a specific snapshot

```
 curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/snapshots/mywvXtaFTesETDLY8wLFek9YCOo?offline=true' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool
| grep -B5 172.30.179.9

 
Result:

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   338  100   338    0     0   1762      0 --:--:-- --:--:-- --:--:--  1769
{
    "snapshotId": "zmLGhjnYU-nVDQ8SQDKMNgbr1mM",
    "plugin": "prometheus",
    "from": 1668181807000,
    "tags": [],
    "label": "http://172.30.179.39:3003/metrics",
    "entityId": {
        "host": "06:ab:62:ff:fe:50:33:94",
        "pluginId": "com.instana.forge.infrastructure.custom.prometheus.Prometheus",
        "steadyId": "http://172.30.179.39:3003/metrics"
    },
    "data": {
        "kind": "prometheus"
    }
}

```



## Resquest JSON

To request metrics you'll need a json file to add to the POST curl command

### Rating heap size

vi get-rating-heap.json

```
{
    "metrics":
[
    "metrics.gauges.nodejs_heap_size_used_bytes"
],
"plugin": "prometheus",
"query": "entity.prometheus.gauge"
"rollup": 60,
"snapshotIds": [
    "759HHggWdDEg8jHQJbaIDawabJo"
]
}
```
### Web heap size

vi get-web-heap.json

```
{
    "metrics":
[
    "metrics.gauges.nodejs_heap_size_used_bytes"
],
"plugin": "prometheus",
"query": "entity.prometheus.gauge"
"rollup": 60,
"snapshotIds": [
    "L1VthhP9ivxMvvHIp-G7vPyl8v0"
]
}
```
### Quote Process metrics

vi get-quote-process.json

```
{
    "metrics":
[
    "metrics.counters.process_cpu_seconds_total",
    "metrics.counters.process_cpu_system_seconds_total",
    "metrics.counters.process_cpu_user_seconds_total"
],
"plugin": "prometheus",
"query": "entity.prometheus.gauge",
"rollup": 60,
"snapshotIds": [
    "mywvXtaFTesETDLY8wLFek9YCOo"
]
}
```

## Get Custom Metrics

Getting the actual metrics

### Get Rating Heap Size

```
curl -X POST -H "Content-Type: application/json" -d @/Users/grvuolo/Lab/Instana/custom_metrics_prometheus/get-rating-heap.json https://<instana-url>/api/infrastructure-monitoring/metrics --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool > rating-heap.json
```

### Get Web Heap Size

```
curl -X POST -H "Content-Type: application/json" -d @/Users/grvuolo/Lab/Instana/custom_metrics_prometheus/get-web-heap.json https://<instana-url>/api/infrastructure-monitoring/metrics --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool > web-heap.json
```

### Get Quote Process Metrics

```
curl -X POST -H "Content-Type: application/json" -d @/Users/grvuolo/Lab/Instana/custom_metrics_prometheus/get-quote-process.json https://<instana-url>/api/infrastructure-monitoring/metrics --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool > quote-process.json
```





## Get Infra Topology (Optional)

Get dependencies between infrastructure component. Because we miss some correlation between the metrics and k8s (raised this and dev is working to improve it), I've tried to get it "manually"


```
 curl --request GET 'https://<instana-url>/api/infrastructure-monitoring/topology' --header 'Authorization: apiToken <instana-api-token>' -k |  python -mjson.tool > infra-topology.json
``


