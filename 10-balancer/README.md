# Test Envoy balancer
Простая балансировка трафика

Есть сервис `service1`. У него есть два экземпляра.
Есть Envoy, который управляет трафиком.

Трафик приходит на сервис `envoy`. Далее он отправляется на экземпляры  `service1-1` и `service1-2`. Балансировка Round Robin.

## Конфиг Envoy
- [Описание конфига Envoy](../envoy-config.md)

### Секция static_resources.listeners.filter_chains.filters
Фильтр с балансировкой трафика в пределах одного кластера. 

```yaml
- name: envoy.filters.network.http_connection_manager
  typed_config:
    "@type": type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager
    stat_prefix: ingress_http
    codec_type: AUTO
    route_config:
      name: local_route
      virtual_hosts:
        - name: local_service
          domains: ["*"]
          routes:
            - match: { prefix: "/" }
              route: { cluster: cluster1 }
```

### Секция static_resources.clusters
В этой секции описан один кластер, на который будет идти трафик.
Описана политика балансировки, время соединения.

В секции `load_assignment` описан набор endpoint-ов этого кластера.

```yaml
clusters:
- name: cluster1
  connect_timeout: 0.25s
  type: STRICT_DNS
  lb_policy: ROUND_ROBIN
  load_assignment:
    cluster_name: cluster1
    endpoints:
    - lb_endpoints:
      - endpoint:
          address:
            socket_address:
              address: service1-1
              port_value: 80
      - endpoint:
          address:
            socket_address:
              address: service1-2
              port_value: 80
```
