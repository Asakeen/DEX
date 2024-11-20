# Настройка статических пользователей в Kubeflow

Здесь приведены файлы конфигурации ролей, привязок ролей, профилей статических пользователей для авторизации в веб-интерфейсе Kubeflow через DEX участников команды №2 RoboTech.
Авторизация через сторонних провайдеров авторизации не рассматривается.

## Роли

Предусмотреное разделение ролей:

- cluster-admin-role - администраторы;
- researcher-role - Data scientists;
- data-analyst-role - Дата аналитики.

## Namespaces

Предусмотрено создание namespaces:

- analytics - для привязки к нему профилей дата-аналитиков;
- research - для привязки к нему профилей data scientists.

Привязка пользователей к namespaces указана в Role, ClusterRole, персональных профилях пользователей.

## Применение конфигураций

``` bash
kubectl apply -f namespaces.yaml -f roles-bindings/ -f user-profiles
```

## Модификация configmap DEX

Для выполнения модификации configmap DEX выполнен импорт в файл:

``` bash
 kubectl get configmap dex -n auth -o jsonpath='{.data.config\.yaml}' > dex-upd.yaml
```

В полученный файл в секцию staticPassword: добавлены записи о пользователях.

Обновление конфигурации на сервере:

``` bash
kubectl create configmap dex --from-file=config.yaml=dex-upd.yaml -n auth --dry-run=client -o yaml | kubectl apply -f -
```

Перезапуск DEX с новой статической конфигурацией

``` bash
kubectl rollout restart deployment dex -n auth
```
