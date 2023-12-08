# Конфиг Envoy
Начальные сведения об устройстве конфига Envoy.

На верхнем уровне есть секция `admin` и `static_resources`.

```yaml
admin:
  ...
static_resources:
  listeners:
    ...
  clusters:
    ...
```

## Секция admin

```yaml
admin:
  address:
    socket_address:
      protocol: TCP
      address: 127.0.0.1
      port_value: 9901
```
В этом репозитории секция `admin` не меняется и рассматриваться не будет.

## Секция static_resources
Внутри секции есть два раздела:
- listeners
- clusters

### Секция static_resources.listeners
В секции `listeners` описывается адрес и порт, которые слушает Envoy, filter_chains (цепочка фильтров).
```yaml
address:
  socket_address: { address: 0.0.0.0, port_value: 10000 }
filter_chains:
- filters:
    ...
```

Далее нам будет интересна секция `static_resources.listeners.filter_chains`.

### Секция static_resources.clusters
В этой секции описываются кластера, на которые должен идти трафик.
На уровне отдельного кластера могут быть описаны правила балансировки, healthcheck-и и пр.

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
          ...
      - endpoint:
          ...
```

В секции `load_assignment` описан набор endpoint-ов внутри кластера. 
