# Test Envoy healthcheck
Балансировка трафика с активной проверкой healthcheck.

Есть сервис `service1`. У него есть два экземпляра.
Есть Envoy, который управляет трафиком.

Трафик приходит на сервис `envoy`. Далее он отправляется на экземпляры  `service1-1` и `service1-2`. Балансировка Round Robin.

Если healthcheck выявит нерабочий экземпляр, тогда трафик в этот экземпляр не должен идти. 

## Конфиг Envoy
- [Описание конфига Envoy](../envoy-config.md)
- [Простая балансировка трафика](../10-balancer/README.md)

В качестве основы взят примера [Простая балансировка трафика](../10-balancer).
Тут будут описаны только отличия.

### Секция static_resources.clusters
Добавлено описание healthcheck.

```yaml
...
clusters:
- name: cluster1
  health_checks:
    - timeout: 1s
      interval: 1s
      unhealthy_threshold: 2
      healthy_threshold: 1
      http_health_check:
        path: /ping
...
```
