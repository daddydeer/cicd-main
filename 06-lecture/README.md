# Запуск и настройка gitlab-runner

## Практическое задание

Информации в прошедших лекциях достаточно, чтобы самостоятельно настроить новый раннер и подключить его к своему проекту. Задание можно выполнять на учебном стенде или любой линукс-среде с докером и подключением к интернету.

Сперва создайте новый репозиторий. Затем подключите к нему раннер отвечающий следующим требованиям:

- Управляющий бинарь `gitlab-runner` должен быть запущен в контейнере.
- Раннер должен позволять собирать докер образы в пайплайне.
- В приложенном коде есть конфигурация пайплайна, в котором соберётся контейнер с приложением. Раннер должен запустить этот контейнер на стенде (или на вашем ноутбуке, если вы выполняете задание локально).
- Зарегистрировать раннер необходимо не используя команду `gitlab-runner register`, а через обращение в API гитлаба.
- Нужна возможность также сохранить собранный бинарный файл локально (на стенде). Пусть на хосте это будет директория `/artifacts`.
- Чтобы не решать проблемы связанные со случайным использованием шаренных раннеров, убедитесь, что на репозитории шаренные раннеры отключены.

Использовать стоит следующие материалы:

- Код от предыдущих лекций.
- [Документацию по конфигурационному файлу раннера](https://docs.gitlab.com/runner/configuration/advanced-configuration.html).
- [Документацию по Runners API](https://docs.gitlab.com/ee/api/runners.html).

### Подсказки

<details>
<summary>А как, собственно, регистрировать раннер через API?</summary>

В документации Runners API есть [этот раздел](https://docs.gitlab.com/ee/api/runners.html#register-a-new-runner).

</details>

<details>
<summary>Получил JSON в ответ, а дальше что?</summary>

Там есть токен. Его надо вписать самостоятельно в `config.toml`.

</details>

<details>
<summary>А как конфиг писать?</summary>

Повторите практику второй лекции. В ней будет сгенерирован `config.toml`, который можно взять за основу.

</details>

<details>
<summary>Ок, а как запускать?</summary>

В коде второй лекции есть баш-скрипт для запуска, модифицируйте его.

</details>

<details>
<summary>Раннер запустился, а вот пайплайны не стартуют</summary>

Нужно использовать docker-executor. Также нужно примонтировать в контейнер с управляющим бинарём `/var/run/docker.sock`.

</details>

<details>
<summary>Раннер запустился, пайплайны стартуют, а вот докеры в пайплайне не собираются.</summary>

Доступ до `/var/run/docker.sock` нужен и контейнерам пайплайна. Посмотрите параметр volumes и пример [здесь](https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#use-docker-socket-binding).

</details>

<details>
<summary>Артифакты не сохраняются.</summary>

В `volumes` нужен bind-mount `/artifacts:/artifacts`.

</details>

## Проверка результата

После правильной настройки раннера, скопируйте в его репозиторий содержимое директории `06-lecture/echoserver`. Убедитесь, что скопировали все файлы, в том числе и `.gitlab-ci.yml` (по-умолчанию глоб `*` не захватывает такие имена файлов). Сделайте коммит и пуш в репозиторий и проследите за ходом пайплайна. После этого, на стенде, где был запущен пайплайн, должен появится бинарь `/artifacts/echoserver`, а введя команду

```
curl localhost:58080
```

вы в ответе получите содержимое запроса.