## [REST API](http://localhost:8080/doc)

## Концепция:

- Spring Modulith
    - [Spring Modulith: достигли ли мы зрелости модульности](https://habr.com/ru/post/701984/)
    - [Introducing Spring Modulith](https://spring.io/blog/2022/10/21/introducing-spring-modulith)
    - [Spring Modulith - Reference documentation](https://docs.spring.io/spring-modulith/docs/current-SNAPSHOT/reference/html/)

```
  url: jdbc:postgresql://localhost:5432/jira
  username: jira
  password: JiraRush
```

- Есть 2 общие таблицы, на которых не fk
    - _Reference_ - справочник. Связь делаем по _code_ (по id нельзя, тк id привязано к окружению-конкретной базе)
    - _UserBelong_ - привязка юзеров с типом (owner, lead, ...) к объекту (таска, проект, спринт, ...). FK вручную будем
      проверять

## Аналоги

- https://java-source.net/open-source/issue-trackers

## Тестирование

- https://habr.com/ru/articles/259055/

Список выполненных задач:

1.Удалить социальные сети: vk, yandex
  -  удалил  кнопки из registr.html файла
  - закоментировал в application.yaml секции с VK and Yandex  для временного отключения их с возможностью легко включить обратно.

2.Вынес чувствительную информацию в отдельный проперти файл:
  -  в application.yaml заменил фактические значения на плейсхолдеры 
      пример : 
         datasource:
          postgres: 
           url: ${APP_DATASOURCE_URL:jdbc:postgresql://localhost:5432/jira}
           username: ${APP_DATASOURCE_USERNAME}
           password: ${APP_DATASOURCE_PASSWORD}
           driver-class-name: org.postgresql.Driver 
 - в IDEA в проекте в настройках Edit Configurations в секции Environment variables прописал плейсхолдеры 
  например:
     APP_DATASOURCE_USERNAME=jira
     APP_DATASOURCE_PASSWORD=JiraRush
     APP_OAUTH_GOOGLE_CLIENT_ID=твой_id_google
     и так далее.

3.Переделать тесты так, чтоб во время тестов использовалась in memory БД (H2), а не PostgreSQL
  - Добавь зависимость H2.
  - Создал DataSourceConfig.java.
  - Обновил основной application.yaml (добавил префикс postgres к настройкам datasource).
  - Обновил application-test.yaml с настройками для H2 (префикс h2 для datasource, ddl-auto: create-drop, MODE=PostgreSQL).
  - создал отдельного changelog-test.sql для решения проблем совместимости миграций между разными СУБД (PostgreSQL) и (H2)
  - Тестовый changelog-test.sql  содержит только те миграции, которые нужны для тестов и написаны с учетом синтаксиса и возможностей H2.
  - в test data.sql адоптировал синтаксис под H2
