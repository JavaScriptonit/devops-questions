## Вопросы:
1. Какой executor используется для запуска джобов? Какие бывают? Почему выбран именно docker? 

## 1. Какой executor используется для запуска джобов? Какие бывают? Почему выбран именно docker? (Gitlab Runner)

GitLab Runner поддерживает несколько различных типов executor'ов для запуска джобов. Некоторые из них:

1. **Shell executor:** Использует оболочку командной строки для запуска джобов. Этот executor прост в настройке, но не обеспечивает изоляцию и безопасность контейнеров.

2. **Docker executor:** Запускает каждую джобу в отдельном контейнере Docker. Этот executor обеспечивает изоляцию среды выполнения, легко масштабируется и позволяет использовать готовые образы Docker для выполнения задач.

3. **Kubernetes executor:** Запускает джобы в Kubernetes подах. Этот executor обеспечивает масштабируемость, изоляцию и управление ресурсами в Kubernetes кластере.

4. **SSH executor:** Позволяет запускать джобы на удаленных серверах по SSH. Этот executor полезен для интеграции с legacy системами или для запуска задач на удаленных серверах.

Выбор Docker executor для GitLab Runner часто обусловлен следующими причинами:

- **Изоляция:** Docker обеспечивает изоляцию среды выполнения каждой джобы, что повышает безопасность и предотвращает конфликты между задачами.
- **Портативность:** Docker образы легко переносимы и могут быть использованы на разных платформах, что облегчает развертывание и управление GitLab Runner.
- **Удобство:** Docker позволяет использовать готовые образы с необходимыми инструментами и зависимостями, упрощая настройку и выполнение джобов.

Таким образом, выбор Docker executor для GitLab Runner обычно обусловлен его удобством, изоляцией и портативностью, что делает его популярным выбором для запуска джобов в CI/CD pipeline.

