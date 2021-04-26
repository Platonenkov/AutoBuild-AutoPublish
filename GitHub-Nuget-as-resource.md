# Добавление источника пакетов в систему

1. Открыть консоль `cmd`
2. Вызвать команду `dotnet nuget list source` для просмотра активных источников пакетов
3. Получить персональный токен доступа в GitHub:
    1. В ВЕБ-интерфейсе зайти в настройки аккаунта `Settings`
    2. Зайти в раздел `Developer settings`
    3. Зайти в раздел `Personal access tokens`
    4. Создать новый токен с доступом `read:packages`
    5. Сохранить токен в защищённом месте. Токен на сайте отображается лишь один раз. Затем его можно лишь регенирировать заново.
5. Добавить источник командой `dotnet nuget add source https://nuget.pkg.github.com/{ProjectName}/index.json -n github.SCAC -u USER -p TOKEN`
6. Проверить, что источник появился в списке источников `dotnet nuget list source`
7. Создать тестовый проект
    1. Создать пустую папку `TestProject` и открыть в ней консоль `cmd`
    2. Создать консольный проект `dotnet new console`
    3. Добавить в проект пакет `dotnet add package {pacageName}`
    4. Проверить, что пакет был добавлен `type TestProject.csproj`
