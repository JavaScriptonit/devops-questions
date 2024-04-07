## Вопросы:
1. Используются ли БД в k8s для имеющихся сервисов? Коннектятся ли БД к каким-то сервисам внутри k8s?
2. Как вносите изменения в БД когда релизите новую версию приложения и для неё нужно изменить табличку?
3. Как обходить блокировки при миграции БД в k8s при 2-3 репликах сервиса?
4. Использовал ли Jobs и Init контейнеры? Для чего? Привести пример
5. Jobs и Init контейнеры в k8s? Как работает сам механизм? Привести пример
6. Может ли Init контейнер настраивать окружение?
7. Для запуска 3х реплик приложения - сколько нужно запустить Init контейнеров? В чарте по описанию - 3 реплики
8. Для чего в промышленном использовании куба может подниматсья Job для приложений?
9. Что будет с configMap в самом кубере?
10. Как в таком случае k8s понимает что нужно удалить configMap?
11. Каким образом он отделяет внутри кубера то что было задеплоино с этим приложением от всего другого?
12. Как вы понимали что релиз удачный? Выводим что-то на прод. Как понять что всё прошло хорошо? 
13. Может быть такая ситуация что "Статус - Deployed", но при этом поды не работают или работают неправильно. Что делать чтобы этого избежать?
14. Ставим 3 реплики приложения. Заходим, а их 5 в кубере. Как будешь понимать какая реплика к какой версии относится? 
15. Ставим 3 реплики приложения. Заходим, а их 5 в кубере. Как такое может произойти при деплое?
16. 

## 1. Используются ли БД в k8s для имеющихся сервисов? Коннектятся ли БД к каким-то сервисам внутри k8s?

1) В Kubernetes (k8s) часто используются базы данных для хранения данных, которые обрабатываются сервисами. БД могут быть запущены внутри кластера Kubernetes для обеспечения отказоустойчивости, масштабируемости и управления данными.

2) Да, базы данных могут подключаться к сервисам внутри кластера Kubernetes. Например, приложения внутри кластера могут использовать DNS имена для обращения к базе данных, которая также запущена в кластере.

Пример поднятия базы данных в Kubernetes с использованием волюма в одном поде и бэкэнд сервиса в другом поде в рамках одного Namespace:

### Шаги для развертывания:

1. **Создание манифестов для базы данных и бэкэнд сервиса**:

   - Создайте манифест для запуска базы данных (например, PostgreSQL) с использованием PersistentVolume и PersistentVolumeClaim для хранения данных.
   - Создайте манифест для запуска бэкэнд-сервиса, который будет подключаться к базе данных.

2. **Применение манифестов**:

   ```bash
   $ kubectl apply -f database-pod.yaml
   $ kubectl apply -f backend-service-pod.yaml
   ```

3. **Обеспечение связи между подами**:
   - В манифесте бэкэнд-сервиса укажите имя базы данных как хост для подключения.
   - Можно использовать DNS имена для обращения к другим сервисам внутри кластера Kubernetes.

Пример манифестов для базы данных и бэкэнд сервиса можно найти ниже:

### Пример манифеста для базы данных (database-pod.yaml):
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: database-pod
spec:
  containers:
  - name: database
    image: postgres
    volumeMounts:
    - mountPath: /var/lib/postgresql/data
      name: database-storage
  volumes:
  - name: database-storage
    persistentVolumeClaim:
      claimName: database-pvc
```

### Пример манифеста для бэкэнд-сервиса (backend-service-pod.yaml):
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: backend-service-pod
spec:
  containers:
  - name: backend-service
    image: backend-service-image
    env:
    - name: DATABASE_HOST
      value: database-pod
```

После применения этих манифестов, база данных и бэкэнд-сервис будут запущены в кластере Kubernetes и смогут общаться друг с другом.

## 2. Как вносите изменения в БД когда релизите новую версию приложения и для неё нужно изменить табличку? (k8s)

Для внесения изменений в базу данных при релизе новой версии приложения в Kubernetes и изменения структуры таблицы, можно использовать подходы, такие как использование миграций баз данных или управляемых решений для управления схемой базы данных.

### Миграции баз данных:
1. **Создание миграций**: Создайте скрипты миграций баз данных, которые будут выполнять необходимые изменения в структуре таблицы (например, добавление новых столбцов, изменение типов данных и т. д.).
2. **Применение миграций**: Включите выполнение этих скриптов миграций в процесс деплоя новой версии вашего бэкэнд-приложения. Например, вы можете использовать инструменты для миграции баз данных, такие как Flyway или Liquibase, которые могут автоматически применять миграции при запуске новой версии приложения.

### Пример использования миграций баз данных:
1. Создайте новый скрипт миграции, который содержит SQL-запросы для изменения структуры вашей таблицы.
2. Обновите манифест вашего бэкэнд-сервиса в Kubernetes, чтобы включить применение этого скрипта миграции при деплое новой версии приложения.
3. После успешного деплоя новой версии приложения, скрипт миграции будет выполнен, и изменения в структуре таблицы будут применены.

### Важно:
- При использовании миграций баз данных, убедитесь в тщательном тестировании скриптов миграций перед их применением в производственной среде.
- Резервируйте резервные копии базы данных перед внесением изменений, чтобы в случае неудачного применения миграции можно было быстро восстановить данные.

После внесения изменений в базу данных с помощью миграций, ваше приложение с новой версией сможет корректно работать с обновленной структурой таблицы.

### Пример миграции:

В качестве примера применения скриптов миграций для изменения структуры таблицы в базе данных MySQL при деплое новой версии приложения в Kubernetes, рассмотрим следующий сценарий:

1. **Создание скрипта миграции**:
   - Создайте новый SQL-скрипт, например, `001_alter_table.sql`, который содержит запросы для изменения структуры таблицы. Например, добавим новый столбец `new_column` в таблицу `example_table`:
     ```sql
     ALTER TABLE example_table
     ADD COLUMN new_column VARCHAR(50);
     ```

2. **Применение миграции при деплое новой версии приложения**:
   - Включите выполнение этого скрипта миграции в процесс деплоя новой версии вашего бэкэнд-приложения в Kubernetes. Для этого в манифесте вашего приложения (например, Deployment) добавьте инициализацию контейнера для выполнения скрипта миграции перед запуском приложения.
   - Пример манифеста Deployment с инициализацией контейнера для выполнения скрипта миграции:
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: backend-app
     spec:
       replicas: 3
       template:
         spec:
           containers:
           - name: backend
             image: your-backend-image:latest
           initContainers:
           - name: migrate
             image: mysql:latest
             command: ["mysql", "-h", "your-mysql-host", "-u", "your-username", "-pYourPassword", "your-database", "-e", "source /path/to/001_alter_table.sql"]
     ```

3. **Применение изменений**:
   - После успешного деплоя новой версии приложения в Kubernetes, скрипт миграции `001_alter_table.sql` будет выполнен, и изменения в структуре таблицы будут применены к базе данных.

### Примечания:
- Убедитесь, что у вас есть доступ к базе данных MySQL и правильно указаны параметры подключения в команде MySQL в манифесте.
- Перед применением миграции в производственной среде, убедитесь в тщательном тестировании скрипта миграции на тестовой базе данных.

Это пример использования скриптов миграций для изменения структуры таблицы при деплое новой версии приложения в Kubernetes. Помните о важности тестирования и резервирования данных перед внесением изменений в базу данных.

## 3. Как обходить блокировки при миграции БД в k8s при 2-3 репликах сервиса? (k8s)
### Миграции БД:
1. 1 из решений - плохое - это подключиться к базе вручную и ручками добавить изменения в базу.
2. 2 из решений - это накатывать из запущенного приложения изменения на базу
Но когда это запускается в кубере то тут зависит от кол-ва экземпляров сервиса.
Если подняты 2-3 реплики и они одновременно пойдут прогонять миграции по БД то могут быть блокировки

При работе с миграциями баз данных в Kubernetes с несколькими репликами сервиса можно столкнуться с проблемой блокировок, если все реплики одновременно пытаются применить миграции к базе данных. Это может привести к конфликтам и нежелательным результатам.

Для обхода блокировок при миграции баз данных в Kubernetes при наличии 2-3 реплик сервиса можно использовать следующие подходы:

1) Одноразовые миграции: Вы можете настроить механизм, который позволит запускать миграции только из одной реплики сервиса. Например, вы можете использовать Job в Kubernetes для запуска миграций и убедиться, что Job будет выполнен только один раз, независимо от количества реплик сервиса.

2) Блокировка базы данных: Вы можете использовать механизм блокировки базы данных, чтобы предотвратить одновременное выполнение миграций из нескольких реплик сервиса. Например, вы можете использовать специальный инструмент для управления блокировками базы данных или самостоятельно реализовать механизм блокировки в коде миграций.

3) Расписание миграций: Вы можете настроить расписание для миграций баз данных, чтобы они запускались последовательно и не конфликтовали между собой. Например, вы можете использовать CronJob в Kubernetes для запуска миграций по расписанию.

Выбор конкретного подхода зависит от особенностей вашей системы и требований к процессу миграции баз данных в Kubernetes. Важно тщательно протестировать выбранный подход перед его внедрением в производственную среду, чтобы избежать возможных проблем и конфликтов.

## 4. Использовал ли Jobs и Init контейнеры? Для чего? Привести пример (k8s)

Jobs и Init контейнеры в Kubernetes используются для выполнения определенных задач в рамках пода или приложения. Вот примеры использования Jobs и Init контейнеров в Kubernetes:

1. **Использование Job для выполнения одноразовой задачи:**

Создадим Job, который будет выполняться один раз и завершаться после выполнения задачи.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
      - name: my-container
        image: busybox
        command: ["echo", "Hello from the Job"]
      restartPolicy: Never
  backoffLimit: 4
```

Запустим Job с помощью команды:
```bash
kubectl apply -f job.yaml
```

2. **Использование Init контейнера для предварительной настройки:**

Создадим Pod с Init контейнером, который будет выполняться перед основным контейнером приложения.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: main-container
    image: nginx
  initContainers:
  - name: init-container
    image: busybox
    command: ['sh', '-c', 'echo "Init container is running"']
```

Запустим Pod с Init контейнером с помощью команды:
```bash
kubectl apply -f pod.yaml
```

3. **Пример использования Init контейнера для инициализации базы данных:**

Создадим Pod с Init контейнером, который будет инициализировать базу данных перед запуском основного приложения.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: db-pod
spec:
  containers:
  - name: db-container
    image: mysql
    env:
      - name: MYSQL_ROOT_PASSWORD
        value: password
  initContainers:
  - name: init-db
    image: mysql
    command: ['sh', '-c', 'mysql -u root -ppassword -e "CREATE DATABASE mydb;"']
```

Запустим Pod с Init контейнером для инициализации базы данных:
```bash
kubectl apply -f db-pod.yaml
```

Это лишь примеры использования Jobs и Init контейнеров в Kubernetes. Важно помнить, что Jobs используются для одноразовых задач, а Init контейнеры для предварительной настройки перед запуском основного контейнера приложения.

## 5. Jobs и Init контейнеры в k8s? Как работает сам механизм? Привести пример (k8s)

Jobs и Init контейнеры - это два различных концепта в Kubernetes, которые используются для выполнения определенных задач в рамках пода или приложения. Давайте рассмотрим их работу более подробно:

1. **Init контейнеры:**

Init контейнеры представляют собой дополнительные контейнеры, которые запускаются перед основным контейнером приложения в рамках одного и того же пода. Они используются для предварительной настройки среды перед запуском основного контейнера. Когда все Init контейнеры завершают свою работу успешно, основной контейнер приложения запускается.

Механизм работы Init контейнеров в Kubernetes:
- Когда Pod создается, Kubernetes запускает все Init контейнеры в порядке их определения в спецификации Pod.
- Каждый Init контейнер выполняет свою задачу и завершается.
- После того как все Init контейнеры успешно завершились, основной контейнер приложения стартует.

2. **Jobs:**

Jobs в Kubernetes используются для выполнения одноразовых задач, которые должны быть выполнены успешно. Job создает один или несколько подов (Pod) и гарантирует, что задача будет выполнена успешно. После завершения задачи, Pod удаляется.

Механизм работы Jobs в Kubernetes:
- Job создает один или несколько подов (Pod), которые выполняют задачу.
- Kubernetes отслеживает состояние выполнения Job и подов.
- После завершения задачи, Pod удаляется, но информация о выполнении задачи сохраняется в истории Job.

Таким образом, Init контейнеры используются для предварительной настройки среды перед запуском основного контейнера приложения, а Jobs используются для выполнения одноразовых задач. Оба эти концепта помогают управлять различными аспектами запуска и выполнения приложений в Kubernetes.

## 6. Может ли Init контейнер настраивать окружение? (k8s)

1) Да, инит контейнер может настраивать окружение внутри пода. Например, он может выполнять следующие задачи:
   - Установка зависимостей и пакетов перед запуском основного приложения.
   - Проверка доступности внешних сервисов или ресурсов, необходимых для работы приложения.
   - Инициализация базы данных или других хранилищ данных перед запуском приложения.

2) Когда говорим о "предварительной настройке среды", мы имеем в виду подготовку условий для запуска основного контейнера приложения. Это может включать в себя настройку файловой системы, установку необходимых инструментов, проверку доступности ресурсов и многое другое.

Пример использования инит контейнера для настройки окружения:
Предположим, у вас есть приложение, которое требует подключения к базе данных перед запуском. Вы можете использовать инит контейнер для инициализации базы данных перед запуском основного контейнера приложения.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: myapp
  initContainers:
  - name: init-db
    image: mysql
    command: ['sh', '-c', 'mysql -u root -ppassword -e "CREATE DATABASE mydb;"']
```

В этом примере инит контейнер запускает команду для создания базы данных MySQL перед запуском основного контейнера приложения. Таким образом, инит контейнер помогает предварительно настроить среду (в данном случае - базу данных) перед запуском приложения.

## 7. Для запуска 3х реплик приложения - сколько нужно запустить Init контейнеров? В чарте по описанию - 3 реплики  (k8s)

Для запуска 3 реплик приложения в Kubernetes, вам необходимо запустить по одному инит контейнеру для каждой реплики. Каждый инит контейнер будет выполняться в каждой реплике перед запуском основного контейнера приложения.

Таким образом, если у вас есть приложение с 3 репликами, вам нужно будет запустить 3 инит контейнера - по одному для каждой реплики. Каждый из этих инит контейнеров будет выполнять задачи предварительной настройки среды перед запуском соответствующей реплики основного контейнера приложения.

## 8. Для чего в промышленном использовании куба может подниматсья Job для приложений? (k8s)

Пример создания Job в Kubernetes:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: example-job
spec:
  template:
    spec:
      containers:
      - name: example-container
        image: busybox
        command: ['echo', 'Hello, Kubernetes!']
      restartPolicy: Never
  backoffLimit: 5
```

В промышленном использовании Kubernetes Job может подниматься для выполнения одноразовых задач, таких как:
- Запуск скриптов обслуживания базы данных или других ресурсов.
- Выполнение регулярных задач по расписанию, таких как резервное копирование данных.
- Обновление индексов или кэшей.
- Выполнение операций по обработке данных или аналитике.

В вашем примере, где у вас есть 3 реплики фронт/бэк приложения в Kubernetes, Job может подниматься для выполнения регулярных задач, таких как очистка временных файлов, обновление кэшей, резервное копирование баз данных и т. д.

Пример конфигурации перезапуска Job и его расписания:
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: data-backup-job
spec:
  template:
    spec:
      containers:
      - name: backup-container
        image: backup-image
        command: ['sh', '-c', 'backup-script.sh']
      restartPolicy: OnFailure
  backoffLimit: 3
  schedule: "0 0 * * *"
```

В этом примере Job будет выполняться каждый день в полночь для выполнения задачи резервного копирования данных. Если Job завершится с ошибкой, он будет перезапущен не более 3 раз. Таким образом, Job будет выполняться каждый день для обеспечения регулярного резервного копирования данных вашего приложения.

## 9. Что будет с configMap в самом кубере? (helm) (k8s)
Есть очень простенький чарт в helm версии 1.0.
Мы там создаём конфиг мапу, создаём configMap.yaml, описываем его, деплоим в кубер.
Далее меняем чарт не в кубере, а у себя в файликах и меняем версию с 1.0 на 1.1 и удаляем эту конфиг мапу, деплоимся еще раз.
Вопрос:
Что будет с configMap в самом кубере?
-----------------------

Если вы изменили ваш Helm чарт локально, удалили конфигмапу и обновили версию чарта с 1.0 на 1.1, затем развернули обновленный чарт в Kubernetes, то следующее произойдет с конфигмапой в самом Kubernetes:

1. При развертывании обновленного чарта с версией 1.1, Helm не создаст конфигмапу, так как она была удалена из вашего чарта.
2. Существующая конфигмапа, которая была создана при развертывании чарта версии 1.0, останется в Kubernetes без изменений.
3. Kubernetes не удалит конфигмапу автоматически, так как она была создана Helm'ом и не управляется им после создания.

Таким образом, конфигмапа, которая была создана при развертывании чарта версии 1.0, останется в Kubernetes после обновления чарта на версию 1.1 и удаления конфигмапы из чарта.

## 10. Как в таком случае k8s понимает что нужно удалить configMap? (helm) (k8s)

При обновлении Helm чарта и удалении конфигмапы из его конфигурации, Kubernetes удалит существующую конфигмапу. 

Kubernetes понимает, что конфигмапа должна быть удалена из-за управления ресурсами через контроллеры ресурсов. При обновлении ресурса Deployment, StatefulSet или других контроллеров, Kubernetes сравнивает текущее состояние ресурсов с новой конфигурацией и применяет необходимые изменения.

Если конфигмапа была создана Helm'ом при развертывании чарта версии 1.0, а затем удалена из чарта версии 1.1, при следующем деплое Helm чарта версии 1.1 Kubernetes обнаружит, что конфигмапа больше не упоминается в конфигурации чарта и удалит ее из кластера.

Таким образом, при обновлении чарта и удалении конфигмапы из его конфигурации, Kubernetes удалит существующую конфигмапу, так как она больше не описывается в конфигурации чарта.

## 11. Каким образом он отделяет внутри кубера то что было задеплоино с этим приложением от всего другого? (helm) (k8s)
У нас есть 2 множества: 1ое - это множество объектов в кубере. В нём может быть больше чем наше 1 приложение и есть множество объектов внутри Чарта, который мы деплоим.
Хелм видит 2ое множество, которое внутри чарта. Но ему нужно как-то сравнить 1ое множество внутри кубера и 2ое множество внутри чарта.
Кубер же не может удалить всё чего нет в нашем чарте (например, что-то поставлено руками, а что-то другими чартами).
Каким образом он отделяет внутри кубера то что было задеплоино с этим приложением от всего другого?
-----------------------

Kubernetes использует концепцию меток (labels) и селекторов (selectors) для управления и организации ресурсов в кластере. Это помогает ему отслеживать и управлять объектами, связанными с конкретным приложением или чартом Helm.

Когда вы развертываете приложение с помощью Helm чарта, вы можете добавить метки к создаваемым ресурсам (например, Deployment, Service, ConfigMap и т. д.). Эти метки могут указывать на принадлежность ресурсов к конкретному приложению или чарту.

При обновлении или удалении ресурсов через Helm, он использует селекторы, чтобы выбрать именно те ресурсы, которые ему нужно обновить или удалить. Это позволяет Helm правильно управлять только теми ресурсами, которые были созданы или изменены через него.

Если какие-то ресурсы были созданы в кластере вне контекста Helm или текущего приложения, Kubernetes не будет автоматически удалять их при обновлении Helm чарта. Однако, правильное использование меток и селекторов помогает отделить ресурсы, созданные в рамках вашего приложения, от других ресурсов в кластере и обеспечить их правильное управление.

### Пример, демонстрирующий как Helm версии 3 добавляет метки к ресурсам, создаваемым в рамках чарта:

В Helm версии 3 информация о ресурсах, созданных в рамках управления чартом, хранится в секрете (Secret) внутри кластера Kubernetes. Этот секрет содержит метаданные о ресурсах, такие как их идентификаторы, типы и метки.

```yaml
# values.yaml
app:
  name: my-app
  replicaCount: 3
```

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Values.app.name }}-deployment
  labels:
    app: {{ .Values.app.name }}
    chart: {{ .Chart.Name }}-{{ .Chart.Version }}
spec:
  replicas: {{ .Values.app.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Values.app.name }}
    template:
      metadata:
        labels:
          app: {{ .Values.app.name }}
      spec:
        containers:
          - name: my-app-container
            image: nginx:latest
```

В этом примере, когда Helm разворачивает чарт, он добавляет метки `app` и `chart` к создаваемому Deployment. Значение метки `app` соответствует имени приложения, указанного в `values.yaml`, а значение метки `chart` содержит имя и версию чарта.

Эти метки помогают Helm отслеживать и управлять ресурсами, созданными в рамках чарта, а также обеспечивают целостность и безопасность управления ресурсами в кластере Kubernetes.

## 12. Как вы понимали что релиз удачный? Выводим что-то на прод. Как понять что всё прошло хорошо? (k8s)

Для команды DevOps важно иметь надежные и эффективные методы анализа успешности релиза при развертывании на продукционную среду в Kubernetes. Вот несколько best practices для анализа успешности релиза:

1. **Health Checks (Проверка состояния)**:
   - Используйте readiness и liveness probes в Kubernetes для проверки состояния приложения после развертывания. Readiness probe позволяет Kubernetes понять, когда приложение готово обслуживать запросы, а liveness probe проверяет, что приложение работает корректно.
  
2. **Мониторинг**:
   - Настройте мониторинг с помощью инструментов, таких как Prometheus и Grafana, для отслеживания производительности, доступности и состояния вашего приложения после развертывания.
  
3. **Логирование**:
   - Собирайте и анализируйте логи приложения и инфраструктуры с помощью инструментов, например Elasticsearch и Kibana, для быстрого выявления проблем и отладки при необходимости.
  
4. **Тестирование**:
   - Автоматизируйте тестирование вашего приложения перед развертыванием на продукционную среду. Включите юнит-тесты, интеграционные тесты и end-to-end тесты, чтобы убедиться в работоспособности приложения.
  
5. **Роллбэк**:
   - Подготовьте план роллбэка в случае неудачного релиза. Используйте механизмы Kubernetes, такие как Deployment Rollback или Helm Rollback, чтобы быстро вернуться к предыдущей стабильной версии приложения.
  
6. **Мониторинг событий**:
   - Следите за событиями и уведомлениями Kubernetes для оперативного реагирования на любые проблемы или нештатные ситуации.
  
7. **Post-Deployment Checks (Проверки после развертывания)**:
   - Проводите проверки после развертывания, чтобы убедиться, что все компоненты приложения работают корректно и соответствуют ожиданиям.

Команда DevOps должна активно использовать эти best practices для анализа успешности релиза на продукционной среде в Kubernetes. Постоянное улучшение и оптимизация процесса развертывания помогут обеспечить стабильную и надежную работу приложения в продакшене.

## 13. Может быть такая ситуация что "Статус - Deployed", но при этом поды не работают или работают неправильно. Что делать чтобы этого избежать? (helm) (k8s)

Чтобы избежать подобных ситуаций и обеспечить надежность развертывания, рекомендуется следующее:

1. **Добавление автоматизированных тестов**: Включите автоматизированные тесты в пайплайн CI/CD, которые будут проверять работоспособность приложения после развертывания. Это могут быть тесты доступности, функциональные тесты, нагрузочное тестирование и т.д.

2. **Использование Readiness и Liveness probes**: Убедитесь, что контейнеры настроены с помощью readiness и liveness probes, чтобы Kubernetes мог мониторить состояние приложения и перезапускать поды в случае проблем.

3. **Мониторинг и логирование**: Настройте мониторинг и логирование для вашего кластера Kubernetes, чтобы оперативно обнаруживать и реагировать на проблемы после развертывания.

4. **Ручная проверка**: Важно также проводить ручные проверки после развертывания, чтобы убедиться, что приложение работает корректно и доступно для пользователей.

Путем комбинации автоматизированных тестов, мониторинга, настройки probes и ручной проверки вы сможете обеспечить более надежное и стабильное развертывание вашего приложения на кластер Kubernetes.

## 14. Ставим 3 реплики приложения. Заходим, а их 5 в кубере. Как будешь понимать какая реплика к какой версии относится? (helm) (k8s)

Для определения, к какой версии относится каждая реплика приложения в Kubernetes, можно использовать метаданные, такие как метки (labels) и аннотации (annotations). В случае использования Helm для управления релизами, Helm автоматически добавляет метаданные к ресурсам Kubernetes, что облегчает определение связи между репликами и версиями приложения.

Вот примеры команд и шагов для проверки связи между репликами и версиями приложения:

1. **Использование kubectl**:
   - Шаг 1: Запустите команду `kubectl get pods` для получения списка всех подов приложения.
   - Шаг 2: Найдите метаданные (labels, annotations) каждого пода, например, с помощью команды `kubectl describe pod <pod_name>`.
   - Шаг 3: Определите, какие метки или аннотации указывают на версию приложения или релиз Helm.

2. **Использование Helm**:
   - Шаг 1: Запустите команду `helm list` для получения списка всех установленных релизов Helm.
   - Шаг 2: Найдите имя релиза, связанного с вашим приложением.
   - Шаг 3: Запустите команду `helm get values <release_name>` для просмотра значений, используемых при установке релиза. Обычно там указывается версия приложения.

Что касается Argo CD, вы также можете использовать его для просмотра информации о развернутых релизах и связанных с ними ресурсах Kubernetes. В Argo CD вы можете просмотреть манифесты, связанные с каждым релизом, и найти информацию о версии приложения, метках и других метаданных.

Таким образом, для определения связи между репликами и версиями приложения в Kubernetes с Helm или Argo CD, вы можете использовать метаданные ресурсов Kubernetes и информацию о релизах, предоставляемую инструментами управления развертыванием.

## 15. Ставим 3 реплики приложения. Заходим, а их 5 в кубере. Как такое может произойти при деплое? (helm) (k8s)
Горизонтальное масштабирование выключено, но мы ставим что всегда хотим видеть 3 реплики этого приложения. Делаем деплой и есть ситуации когда у нас поднимутся 2-3 пода сверху и старые еще не будут убиты.
-----------------------

1) Если при деплое с помощью Helm или Kubernetes у вас появляется больше реплик, чем ожидалось, это может быть вызвано несколькими причинами:

- **Ошибки в манифестах**: Возможно, в манифестах Helm или Kubernetes указано создание большего количества реплик, чем вы ожидали.
- **Проблемы с автомасштабированием**: Если у вас настроено автомасштабирование (Horizontal Pod Autoscaler), это может привести к автоматическому созданию дополнительных реплик в зависимости от нагрузки.
- **Проблемы с контроллерами реплик**: Неправильные настройки контроллеров реплик могут привести к созданию дополнительных реплик.

2) Если горизонтальное масштабирование выключено, но приложение запускает больше реплик, чем ожидалось, это может быть связано с задержкой в остановке старых подов при обновлении. Это может произойти из-за различных факторов, таких как время завершения процесса завершения пода, наличие долгих операций завершения или неправильной конфигурации контроллеров реплик.

Для решения этой проблемы и обеспечения того, что всегда видно только 3 реплики приложения, можно принять следующие меры:

- **Установка максимального числа реплик**: В манифестах Helm или Kubernetes можно указать максимальное количество реплик для контроллера, чтобы избежать появления дополнительных реплик.
- **Мониторинг и логирование**: Отслеживайте количество запущенных реплик и реагируйте на любые аномалии.
- **Использование Readiness и Liveness probes**: Настройте проверки готовности и жизнеспособности для ваших подов, чтобы убедиться, что новые реплики не запускаются до завершения процесса завершения старых.

Принимая эти меры, вы сможете обеспечить стабильное и предсказуемое количество реплик вашего приложения в Kubernetes даже без горизонтального масштабирования.
