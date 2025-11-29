# clickhouse-k8s

---

Данный репозиторий содержит k8s-манифесты и Helm-чарт для развертывания ClickHouse с использованием Altinity ClickHouse Operator.

---


## Требования 

1. Создание single-инсталляции ClickHouse.
2. Должна быть возможность поменять версию ClickHouse.
3. Должна быть возможность указать список пользователей с паролями для ClickHouse.
4. Файлы конфигурации должны быть параметризованы.

---

## Способы развертывания ClickHouse

В Kubernetes ClickHouse можно развернуть несколькими способами:

1. Через обычные **k8s-манифесты / StatefulSet и тд**
2. Через **Altinity ClickHouse Operator** (в целом через операторы)

Выбор пал на **Altinity ClickHouse Operator**, так как он удобнее, надёжнее и предоставляет кастомные ресурсы, что значительно облегчает управление ClickHouse и его развертыванием по сравнению с ручным использованием StatefulSet.

---

## Установка Altinity ClickHouse Operator

Так как у меня есть helm, то установим через него.

```bash
helm repo add clickhouse-operator https://docs.altinity.com/clickhouse-operator
helm upgrade --install --create-namespace \
    --namespace clickhouse \
    clickhouse-operator \
    clickhouse-operator/altinity-clickhouse-operator
```

## Развертывание ClickHouse с помощью k8s-манифестов

> ⚠️ Манифесты можно посмотреть [здесь.](./manifests)


Развертываем секреты (содержат пароли для наших юзеров)
```bash
kubectl apply -f clickhouse-secret.yaml
```

Развертываем ClickHouse
```bash
kubectl apply -f clickhouse.yaml
```

Удаление ClickHouse 
```bash
kubectl delete -f clickhouse.yaml
kubectl delete -f clickhouse-secret.yaml
```
---

Изменять/добавлять пользователей и изменять версию образа можно только ручками в манифестах

---

## Параметризация и развертывание ClickHouse с помощью helm-chart

> ⚠️ Чарт можно посмотреть [здесь.](./charts/clickhouse-chi)

Развертываем ClickHouse с помощью чарта
```bash
helm upgrade --install my-release ./charts/clickhouse-chi/ --namespace clickhouse
```

Изменение версии образа (можем так менять все, что есть в values)
```bash
helm upgrade --install my-release ./charts/clickhouse-chi/ --namespace clickhouse --set image.version=22.8
```

Откат к определенной версии (к примеру к первой)
```bash
helm rollback my-release 1 --namespace clickhouse
```

Удаление чарта
```bash
helm uninstall my-release --namespace clickhouse
```

---

Изменять/добавлять пользователей и изменять версию образа можно через --set при использовании хелма, либо в файле values в чарте

---
