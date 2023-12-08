# Test Envoy router
Балансировка трафика + маршрутизация по методам. 

Есть два сервиса `service1` и `service2`. В каждом по два экземпляра.
Есть Envoy, который управляет трафиком.

Трафик приходит на сервис `envoy`.
Далее он отправляется в кластер `service1` или `service2`. Внутри кластера выбирается доступный экземпляр.

Кластер выбирается в зависимости от url.

## Конфиг Envoy
- [Описание конфига Envoy](../envoy-config.md)
- [Простая балансировка трафика](../10-balancer/README.md)

В качестве основы взят примера [Простая балансировка трафика](../10-balancer).
Тут будут описаны только отличия.

### Секция static_resources.listeners.filter_chains.filters

```yaml
...
virtual_hosts:
  - name: local_service
    domains: ["*"]
    routes:
      - match: { prefix: "/service/1" }
        route: { cluster: cluster1, prefix_rewrite: "/" }
      - match: { prefix: "/service/2" }
        route: { cluster: cluster2, prefix_rewrite: "/" }
```

Нужно обратить внимание на свойства `prefix` и `prefix_rewrite`.
Думаю, что их назначение понятно.

### Секция static_resources.clusters
Добавлено описание второго кластера

```yaml
...
- name: cluster2
  connect_timeout: 0.25s
  type: STRICT_DNS
  lb_policy: ROUND_ROBIN
  load_assignment:
    cluster_name: cluster2
    endpoints:
      - lb_endpoints:
          - endpoint:
              address:
                socket_address:
                  address: service2-1
                  port_value: 80
          - endpoint:
              address:
                socket_address:
                  address: service2-2
                  port_value: 80
```
