## Вопросы:
1. Работал ли с балансировщиками HAProxy, Nginx?
2. 

## 1. Работал ли с балансировщиками HAProxy, Nginx?
https://habr.com/ru/companies/selectel/articles/250201/

    `HAProxy` и `Nginx` могут использоваться в качестве балансировщиков нагрузки для распределения трафика между несколькими серверами. Они устанавливаются и разворачиваются на серверах с помощью стандартных инструментов управления пакетами, таких как apt или yum для Linux. После установки конфигурируются путем редактирования соответствующих конфигурационных файлов.

    1. Установка `HAProxy`:
    ```
    sudo apt-get update
    sudo apt-get install haproxy
    ```

    2. Конфигурирование HAProxy:
    Конфигурационные файлы HAProxy обычно настраиваются в файле /etc/haproxy/haproxy.cfg. В этом файле определяются настройки балансировки нагрузки, такие как адреса серверов, порты, алгоритмы балансировки и т. д.

    3. После внесения изменений в конфигурационный файл HAProxy перезапускается для применения изменений:
    ```
    sudo systemctl restart haproxy
    ```

    Пример использования Nginx в Kubernetes:

    1. Установка Nginx в Kubernetes:
    Nginx может быть использован как балансировщик нагрузки в Kubernetes с помощью Ingress Controller, такого как Nginx Ingress Controller. Установка Ingress Controller может быть выполнена с использованием Helm:
    ```
    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
    helm install my-release ingress-nginx/ingress-nginx
    ```

    2. Конфигурирование Nginx в Kubernetes:
    После установки Ingress Controller, конфигурация балансировки нагрузки определяется с помощью Ingress-ресурсов Kubernetes, которые определяют правила маршрутизации трафика на службы внутри кластера.

