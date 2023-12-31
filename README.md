# K8s-Docker-Ansible
Этот репозиторий содержит автоматизированный набор инструментов на Ansible для развертывания и управления кластерами Kubernetes и контейнерами Docker. Используйте эти скрипты для быстрого создания и настройки среды Kubernetes с Docker в качестве контейнеризации. Упростите процесс установки и конфигурации Kubernetes и Docker с помощью этого проекта.

# Ansible Kubernetes и Docker установка

Этот проект содержит Ansible-скрипт для автоматизированной установки Kubernetes и Docker на серверах, использующих `Rocky Linux 8`. Скрипт включает в себя следующие шаги:

- Установка необходимых пакетов и обновление системы.
- Установка и настройка Docker CE.
- Установка Kubernetes с указанием версий компонентов.
- Настройка системы, включая отключение swap и изменение параметров ядра.

## Использование

1. Убедитесь, что у вас установлен Ansible на вашем управляющем хосте.
2. Склонируйте этот репозиторий на ваш управляющий хост.
3. Внесите необходимые изменения в файл `hosts`, чтобы определить целевые хосты.
4. Запустите Ansible-скрипт с помощью команды:
```
ansible-playbook k8s-docker-install.yaml
```
## Предостережения

- Убедитесь, что ваши целевые хосты соответствуют требованиям Kubernetes.
- Этот скрипт предназначен для использования в тестовых или разработочных средах.


## Установка и Настройка Grafana и Prometheus

Проект включает Ansible-плейбук для настройки систем мониторинга Grafana и Prometheus в кластере Kubernetes. Плейбук `prometheus-grafana-setup.yaml` выполняет следующие действия:

1. Настраивает Prometheus с использованием Helm.
2. Настраивает Grafana с использованием Helm.
3. Импортирует дашборды Grafana, указанные в JSON-конфигурации.

Установка и подготовка самих Prometheus и Grafana для работы в Kubernetes выполняется отдельно. Убедитесь, что Prometheus и Grafana уже установлены и настроены на ваших серверах перед запуском плейбука.

### Шаг 1: Установка Helm

Для установки Helm 3 на вашем сервере `Rocky Linux 8` с использованием `wget`, выполните следующие шаги:

1. Скачайте архив с Helm 3:

```
wget https://get.helm.sh/helm-v3.7.0-linux-amd64.tar.gz
```

Распакуйте архив и переместите исполняемый файл helm в каталог, доступный в вашей системе:
```
tar -zxf helm-v3.7.0-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```
Убедитесь, что замените 3.7.0 на актуальную версию Helm, если это необходимо.

### Шаг 2: Настройка Kubernetes
Удостоверьтесь, что ваш кластер Kubernetes работает, и неймспейс monitoring существует. Если его нет, вы можете создать его с помощью команды:
```
kubectl create namespace monitoring
```

### Запуск плейбука
Для настройки Grafana и Prometheus в Kubernetes с использованием плейбука, используйте следующую команду:
```
ansible-playbook prometheus-grafana-setup.yaml
```

**Примечание**: С задачами Ansible для Grafana и Prometheus можно ознакомиться в папке "monitoring" внутри репозитория.

```yaml
- name: Импортировать дашборды Grafana
  uri:
    url: http://grafana.monitoring.svc.cluster.local/api/dashboards/import
    method: POST
    body_format: json
    body: '{
      "dashboard": {
        "editable": true,
        "gnetId": null,
        "id": null,
        "panels": [...],  // Описание панелей дашборда
        "refresh": "10s",
        "schemaVersion": 16,
        "style": "dark",
        "tags": [],
        "templating": {
          "list": [...],  // Переменные для шаблонов
          "enable": true
        },
        "title": "My Dashboard",
        "timezone": "browser",
        "uid": "my-dashboard",
        "version": 8
      },
      "overwrite": false
    }'  # JSON-конфиг для импорта дашбордов
    status_code: 200
```

**Замените [...] на реальные данные и настройки для ваших дашбордов.**

### Импорт Готовой Панели управления в Grafana

1. Перейдите в раздел "Панели управления" (Dashboard) в Grafana.

2. Выберите пункт меню "Manage" (Управление) на верхней панели.

3. В меню "Manage" выберите "Import" (Импорт).

4. Нажмите на кнопку "Upload .json file" (Загрузить .json файл) и выберите загруженный JSON-файл, который вы скачали по [ссылке](https://grafana.com/grafana/dashboards/12486-node-exporter-full/).

5. Нажмите "Import" (Импорт) для загрузки и создания панели управления.

Теперь у вас должна быть полная панель управления Prometheus Node Exporter, которая предоставляет множество деталей о активности процессора, диска и сети.

### Полная Панель управления Prometheus Node Exporter

Эта панель управления является ответвлением превосходной панели экспортера узлов 1860 и предоставляет обширную информацию об активности процессора, диска и сети на ваших узлах.



## Лицензия

Этот проект лицензирован в соответствии с [лицензией GPL-3.0](LICENSE).

