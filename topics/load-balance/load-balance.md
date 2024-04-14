## Вопросы:
1. Работал ли с балансировщиками HAProxy, Nginx?
2. С какими веб серверами приходилось работать? Какие есть еще кроме nginx?
3. Nginx у нас на сервере как балансировщик нагрузки. Есть 4 бэк сервера, на которых развёрнуто одинаковое приложение. Какие есть способы балансировки запросов между бэк серверами?
4. Приходилось ли настраивать CORS (Cross-Origin Resource Sharing)? Что это? 

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

## 2. С какими веб серверами приходилось работать? Какие есть еще кроме nginx?

- Apache HTTP Server
- Microsoft IIS (Internet Information Services)
- LiteSpeed Web Server
- Caddy
- OpenLiteSpeed
- Apache Tomcat (для Java приложений)
- и другие.

Каждый из этих веб-серверов имеет свои особенности и подходит для разных типов приложений и сценариев использования.

## 3. Nginx у нас на сервере как балансировщик нагрузки. Есть 4 бэк сервера, на которых развёрнуто одинаковое приложение. Какие есть способы балансировки запросов между бэк серверами?

Для балансировки запросов между бэкенд серверами с помощью Nginx в качестве балансировщика нагрузки, можно использовать различные методы балансировки, такие как round-robin, least connections, ip-hash и другие.

Пример команд и шагов для настройки балансировки запросов с использованием Nginx:

- Установите Nginx на сервере, если он еще не установлен.
- Отредактируйте конфигурационный файл Nginx (обычно располагается в `/etc/nginx/nginx.conf` или `/etc/nginx/sites-available/default`) и добавьте блок для балансировки нагрузки:

```nginx
http {
    upstream backend {
        server backend1:80;
        server backend2:80;
        server backend3:80;
        server backend4:80;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

- Перезапустите Nginx для применения изменений:

```bash
sudo systemctl restart nginx
```

После выполнения этих шагов, Nginx будет балансировать запросы между четырьмя бэкенд серверами, на которых развернуто одинаковое приложение. Вы можете настроить и другие параметры балансировки в соответствии с вашими потребностями.

## 4. Приходилось ли настраивать CORS (Cross-Origin Resource Sharing)? Что это?

CORS (Cross-Origin Resource Sharing) - это механизм, который позволяет веб-странице запрашивать ресурсы с другого источника (домена, протокола или порта), отличного от источника, с которого была загружена сама страница. Это важная мера безопасности, которая помогает предотвратить атаки с использованием скриптов на стороне клиента.

Пример настройки CORS в Nginx:

1. Откройте конфигурационный файл Nginx (обычно располагается в `/etc/nginx/nginx.conf` или `/etc/nginx/sites-available/default`).

2. Добавьте следующие строки для разрешения CORS для всех ресурсов:

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
    }
}
```

3. Перезапустите Nginx для применения изменений:

```bash
sudo systemctl restart nginx
```

Теперь сервер Nginx будет отправлять заголовки CORS в ответ на запросы, разрешая доступ к ресурсам с других источников.
