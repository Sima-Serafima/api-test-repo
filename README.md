# api-test-repo Documentation
# Коллекция Postman ATT

Коллекция создана для проверки 4 методов для работы с issue GitHub:
* Создание issue
* Получение списка issue
* Редактирование issue
* Удаление issue

## Переменные

Перед созданием запросов я создала несколько переменных:
* `token` - содержит API ключ для авторизации
* `userName` - содержит имя пользователя
* `repoName` - содержит название репозитория.

  ### Headers:

```json
{
  "Authorization": "{{token}}",
  "Content-Type": "application/vnd.github+json"
}
```
 * Bearer включен в переменную

## 1. Создание issue

Для создания issue используется метод POST. Запрос отправляется на URL `https://api.github.com/repos/{{userName}}/{{repoName}}/issues`, где `{{userName}}` - это владелец репозитория, а `{{repoName}}` - название репозитория.


### Body:

```json
{
  "title": "issue 1",
  "body": "Something went wrong",
  "assignees": ["Sima-Serafima"],
  "labels": ["bug"]
}
```

## 2. Получение списка issue

Для получения списка issue в соответствии с документацией используется метод GET. Запрос отправляется на URL `https://api.github.com/repos/{{userName}}/{{repoName}}/issues`. При выполнении этого запроса мы получаем список всех issues репозитория. 

Для удобства отправки дальнейших запросов на редактирование и удаление issue из полученного списка issue был написан скрипт, который собирает issue `number` и `node_id` и добавляет их в переменные коллекции: `issueNumber`,`issueGQLNode`. 
В тестовых целях был взят только первый по порядку issue из списка.

## 3. Редактирование issue

Для изменения issue в соответствии с документацией GitHub используется метод PATCH. Запрос отправляется на URL `https://api.github.com/repos/{{userName}}/{{repoName}}/issues/{{issueNumber}}`. Изменяем `title` в теле запроса в соответствии с заданием.

## 4. Удаление issue

В документации GitHub отсутствует информация об удалении issues при помощи API и стандартного метода DELETE. В ходе поиска способа удаления issue при помощи API, я попробовала воспроизвести в Postman запрос, который отображался в DevTools браузера - POST. Не помогло.

В результате поисков был найден способ удаления issue с помощью GraphQL API. В данном случае был использован `node_id`, полученный в запросе списка issues.

### URL:

`https://api.github.com/graphql`

### Body:

```json
{
  "query": "mutation { deleteIssue(input: { issueId: \"{{issueGQLNode}}\", clientMutationId: \"test issue delete\" }) { clientMutationId } }"
}

