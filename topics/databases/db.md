## Вопросы:
1. Для всех типов БД требуется миграция в таком случае или какие-то БД могут самостоятельно изменить табличку и не потерять текущие данные?
2. Реляционные и нереляционные БД. К каким из этих БД будут применимы .sql скрипты? Какие существую UI менеджеры для БД? Какого менеджера можно поднять бесплатно в докер чтобы менеджить эти БД?
3. Назовите noSQL БД.
4. Как разворачивал PostgreSQL без k8s?
5. Как разворачивал PostgreSQL без k8s и docker?
6. Как была развёрнута Mongo? Это был кластер серверов или на 1ом сервере? 
7. С какими БД менеджерами работал? 
8. 


## 1. Для всех типов БД требуется миграция в таком случае или какие-то БД могут самостоятельно изменить табличку и не потерять текущие данные? Если нужно задеплоить новую версию бэкэнда и внести изменения в бд (БД)

Некоторые базы данных, такие как MongoDB, могут автоматически обновлять схему коллекций при добавлении новых полей или индексов без необходимости явного использования скриптов миграции. Однако, для других типов баз данных, таких как SQL базы данных (например, MySQL, PostgreSQL, SQL Server), часто требуется явное использование скриптов миграции для изменения структуры таблиц.

В любом случае, при деплое новой версии бэкенд-приложения с изменениями в базе данных, рекомендуется использовать механизмы контроля версий и миграций баз данных для обеспечения безопасного и надежного обновления структуры базы данных. Такой подход позволяет контролировать изменения, проводить откат в случае неудачного обновления и обеспечивать согласованность данных.

Если ваша база данных поддерживает автоматическое обновление структуры без необходимости миграции, убедитесь, что это поведение соответствует вашим требованиям по безопасности и целостности данных. В любом случае, рекомендуется тщательно тестировать процесс обновления базы данных перед его применением в производственной среде.

Помните, что хорошо спланированное и протестированное обновление базы данных вместе с новой версией приложения поможет избежать потери данных и обеспечит стабильную работу системы.

## 2. Реляционные и нереляционные БД. К каким из этих БД будут применимы .sql скрипты? Какие существую UI менеджеры для БД? Какого менеджера можно поднять бесплатно в докер чтобы менеджить эти БД?

1) Разделение баз данных по типам:

Реляционные базы данных:
- SQL
- MySQL
- PostgreSQL

Нереляционные базы данных:
- Cassandra
- MongoDB
- Redis
- etcd
- ZooKeeper

2) Применимость .sql скриптов:
- Реляционные базы данных (SQL, MySQL, PostgreSQL) обычно используют .sql скрипты для миграции данных и изменения схемы таблиц.
- Нереляционные базы данных (Cassandra, MongoDB, Redis, etcd, ZooKeeper) обычно не используют .sql скрипты для миграции, так как они имеют более гибкую структуру данных и не требуют схемы в традиционном понимании.

3) UI менеджеры для баз данных:
Есть множество UI-менеджеров для различных баз данных, включая реляционные и нереляционные. Некоторые из популярных UI-менеджеров включают:
- DBeaver
- MySQL Workbench
- pgAdmin (для PostgreSQL)
- DataGrip
- Robo 3T (для MongoDB)
- RedisInsight (для Redis)

Для удобства управления различными базами данных в Docker, можно использовать универсальные инструменты управления базами данных, такие как DBeaver или DataGrip, которые поддерживают широкий спектр баз данных. Эти инструменты можно легко запустить в контейнерах Docker для управления базами данных из одного места.

Например, вы можете запустить DBeaver в Docker контейнере следующим образом:
```bash
docker run -it --rm -v ~/dbeaver:/work -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix dpage/pgadmin4
```

## 3. Назовите noSQL БД. (БД)

Разделение баз данных по типам:

1. Реляционные базы данных:
- SQL
- MySQL
- PostgreSQL

2. Нереляционные базы данных:
- Cassandra
- MongoDB
- Redis
- etcd
- ZooKeeper

## 4. Как разворачивал PostgreSQL без k8s? (БД)

Для развертывания PostgreSQL без Kubernetes, но с использованием Docker на сервере Ubuntu, вам потребуется следующий набор шагов:

1) **Установка Docker на сервер Ubuntu:**

```bash
$ sudo apt update
$ sudo apt install docker.io
$ sudo systemctl start docker
$ sudo systemctl enable docker
```

2) **Создание каталога для хранения данных PostgreSQL:**

```bash
$ mkdir -p /opt/postgresql/data
```

3) **Создание и настройка файла конфигурации PostgreSQL:**

```bash
$ touch /opt/postgresql/postgresql.conf
```

Пример содержимого `postgresql.conf`:

```plaintext
listen_addresses = 'localhost'
port = 5432
max_connections = 100
```

4) **Запуск контейнера PostgreSQL:**

```bash
$ sudo docker run -d --name postgresql-container -v /opt/postgresql/data:/var/lib/postgresql/data -v /opt/postgresql/postgresql.conf:/etc/postgresql/postgresql.conf -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 postgres
```

5) **Подключение к контейнеру PostgreSQL:**

```bash
$ sudo docker exec -it postgresql-container psql -U postgres
```

6) **Создание роли и базы данных:**

```sql
CREATE ROLE qa_user WITH LOGIN ENCRYPTED PASSWORD 'qa-pg-pass';
CREATE DATABASE qa_db OWNER qa_user;
```

7) **Подключение к базе данных с новым пользователем:**

```bash
$ sudo docker exec -it postgresql-container psql -U qa_user -d qa_db
```

8) **База данных PostgreSQL успешно развернута на сервере Ubuntu с использованием Docker!**

Эти шаги помогут вам развернуть PostgreSQL на сервере Ubuntu при помощи Docker.

## 5. Как разворачивал PostgreSQL без k8s и docker? (БД)

Для развертывания PostgreSQL без использования Kubernetes и Docker на сервере Ubuntu, вам потребуется установить PostgreSQL напрямую на сервер. Вот инструкция с шагами и командами:

1) **Установка PostgreSQL на сервер Ubuntu:**

```bash
$ sudo apt update
$ sudo apt install postgresql postgresql-contrib
```

2) **Настройка файла конфигурации PostgreSQL:**

```bash
$ sudo nano /etc/postgresql/<version>/main/postgresql.conf
```

Пример настроек в `postgresql.conf`:

```plaintext
listen_addresses = 'localhost'
port = 5432
max_connections = 100
```

3) **Настройка файлов pg_hba.conf для разрешения подключений:**

```bash
$ sudo nano /etc/postgresql/<version>/main/pg_hba.conf
```

Пример настройки в `pg_hba.conf`:

```plaintext
host    all             all             127.0.0.1/32            md5
```

4) **Перезапуск PostgreSQL для применения изменений:**

```bash
$ sudo systemctl restart postgresql
```

5) **Подключение к PostgreSQL и создание роли и базы данных:**

```bash
$ sudo -u postgres psql
```

```sql
CREATE ROLE qa_user WITH LOGIN ENCRYPTED PASSWORD 'qa-pg-pass';
CREATE DATABASE qa_db OWNER qa_user;
```

6) **Подключение к базе данных с новым пользователем:**

```bash
$ psql -U qa_user -d qa_db
```

7) **База данных PostgreSQL успешно развернута на сервере Ubuntu без использования Kubernetes и Docker!**

Эти шаги помогут вам установить и настроить PostgreSQL на сервере Ubuntu напрямую, без использования контейнеров или оркестраторов.

## 6. Как была развёрнута Mongo? Это был кластер серверов или на 1ом сервере? (БД)

Для поднятия кластера базы данных MongoDB на нескольких серверах с использованием Docker и настройки репликации с монтированием директорий, вам понадобится следовать следующим шагам:

1. **Создание docker-compose.yaml:**

```yaml
version: '3'

services:
  mongodb1:
    image: mongo
    container_name: mongodb1
    ports:
      - "27017:27017"
    volumes:
      - mongodb1_data:/data/db
    networks:
      - mongo-cluster
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password

  mongodb2:
    image: mongo
    container_name: mongodb2
    ports:
      - "27018:27017"
    volumes:
      - mongodb2_data:/data/db
    networks:
      - mongo-cluster
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password

  mongodb3:
    image: mongo
    container_name: mongodb3
    ports:
      - "27019:27017"
    volumes:
      - mongodb3_data:/data/db
    networks:
      - mongo-cluster
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password

volumes:
  mongodb1_data:
  mongodb2_data:
  mongodb3_data:

networks:
  mongo-cluster:
```

2. **Репликация кластера MongoDB:**
   Да, MongoDB поддерживает кластеризацию и репликацию данных. Для настройки кластерной репликации MongoDB, вам нужно будет настроить конфигурацию репликации в каждом узле кластера и указать узлы, которые будут участвовать в репликации.

   - Вы можете настроить репликацию с помощью команды `rs.initiate()` для инициализации репликационного набора и добавления узлов в него.
   - MongoDB также предоставляет механизм автоматического обнаружения и восстановления для обеспечения высокой доступности и отказоустойчивости.

3. **Создание единого пользователя с правами администратора на каждом узле кластера:**
```
$ docker exec -it mongodb1 mongo admin --eval "db.createUser({ user: 'admin', pwd: 'password', roles: [ { role: 'root', db: 'admin' } ] })"
$ docker exec -it mongodb2 mongo admin --eval "db.createUser({ user: 'admin', pwd: 'password', roles: [ { role: 'root', db: 'admin' } ] })"
$ docker exec -it mongodb3 mongo admin --eval "db.createUser({ user: 'admin', pwd: 'password', roles: [ { role: 'root', db: 'admin' } ] })"
```

4. Для настройки репликации данных между узлами кластера MongoDB в Docker, нужно настроить репликацию между узлами. Нужно настроить каждый узел как член репликационного набора и указать основной узел (primary) для записи данных, а также вторичные узлы (secondary) для чтения данных командой `rs.initiate()`:

```bash
$ docker exec -it mongodb1 mongo
> rs.initiate()
> rs.add("mongodb2:27017")
> rs.add("mongodb3:27017")
```

Это настроит репликацию между узлами `mongodb1`, `mongodb2` и `mongodb3`, и данные будут автоматически синхронизироваться между ними.

Таким образом, после настройки репликации данные будут одинаковыми в каждой базе MongoDB, и изменения данных будут автоматически синхронизироваться между узлами кластера.

# 7. С какими БД менеджерами работал? (БД)

Существуют различные сервисы для управления базами данных MongoDB и PostgreSQL в облаке. Некоторые из наиболее популярных сервисов:

1. **MongoDB Atlas**:
   - MongoDB Atlas - это управляемый сервис баз данных MongoDB, предоставляемый компанией MongoDB. Он позволяет развернуть кластер MongoDB в облаке, обеспечивает автоматическое масштабирование, резервное копирование данных, мониторинг и многое другое.
   - Для использования MongoDB Atlas вам нужно зарегистрироваться на сайте MongoDB Atlas, создать проект, кластер и настроить его параметры через веб-интерфейс.

2. **Amazon RDS (Relational Database Service) для PostgreSQL**:
   - Amazon RDS - это управляемый сервис реляционных баз данных от Amazon Web Services (AWS), который включает в себя поддержку PostgreSQL, MySQL, Oracle, SQL Server и других.
   - Для использования Amazon RDS для PostgreSQL вам нужно создать экземпляр базы данных PostgreSQL через консоль управления AWS, указав параметры экземпляра, такие как тип экземпляра, размер хранилища, имя пользователя и пароль.

Для установки и использования MongoDB Atlas и Amazon RDS для PostgreSQL не требуется установка на локальной машине. Вам нужно просто зарегистрироваться на соответствующем сервисе, создать и настроить вашу базу данных через веб-интерфейс.

